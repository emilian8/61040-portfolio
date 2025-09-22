# Problem Set 2

## Concept Questions

### 1. Contexts
Contexts partition the nonce “namespace” so uniqueness is enforced within each partition but not across them. That lets different tenants/domains/prefixes reuse the same short token without collisions.
The natural context is the short URL base, such as Context = shortUrlBase or Context = (shortUrlBase, optionalPathPrefix). The result is that “abc123” can exist under multiple bases without conflict because each base is its own context.

### 2. Storing used strings
The spec’s used set of Strings is what enforces the principle “each generate returns a string not returned before.” It’s the abstract way to state “no duplicates.” A common implementation keeps a counter per context and maps integers to strings with an encoder. A possible abstraction function for a context with counter value would be: AF(counter = n).used = { encode(i) | i ∈ {0, 1, ..., n-1} }. The rep invariant is n >= 0, and generate does result := encode(n); n := n+1

### 3. Words as nonces
Advantage: Memorability/typability. green-zebra-harbor is easier to understand than a9FQk3.
Disadvantage: Guessability/privacy. Real words are easier to brute-force and overall less secure. Word sets would also run out faster than random characters.
To adjust the original NonceGeneration I would add a policy field with the following changes: 

Add to state per context: mode ∈ {ENCODED, WORDS}, dictionary, k, separator.

Refine generate:
if mode = ENCODED: behave as counter.

if mode = WORDS: form a k-word phrase from dictionary not in used.


## Synchronization Questions


### 1. Partial Matching
In the first sync, we only match on shortUrlBase because that’s all we need to generate a nonce. The actual targetUrl isn’t needed yet. In the second sync, we’re actually creating the full short link, so we need both the base and the target URL to pass into UrlShortening.register.

### 2. Omitting Names
The shortcut rule only works when the argument name and the variable name line up. If they don’t, we have to write it out explicitly. That way it’s clear what’s being passed where and avoids confusion.

### 3. Inclusion of Request
The first two syncs are tied directly to a user request (Request.shortenUrl). They need that trigger to connect the user action to generating a nonce and then registering it. The third sync is about automatically setting an expiry once something’s registered. This happens regardless of how the URL got registered.

### 4. Fixed Domain
If the service always used something like bit.ly, we’d just hardcode that value. So instead of taking in shortUrlBase from the request, we’d write context: "bit.ly" in the generate sync, and shortUrlBase: "bit.ly" in the register sync. The request wouldn’t need to pass a base at all.

### 5. Adding a sync
A possible sync could be:
sync expireAndDelete
when ExpiringResource.expireResource (): (resource: shortUrl)
then UrlShortening.delete (shortUrl)


## Extending The Design

### 1. Concepts
First:
````
concept ShorteningOwnership [User]

purpose tie each short URL to the user who created it (for access control)
principle only the owner of a short URL can view its analytics
state

a set of Ownerships with

shortUrl String

owner User
actions

assignOwner (shortUrl: String, owner: User)
effect: records that owner owns shortUrl

getOwner (shortUrl: String): (owner: User)
effect: returns the owner for shortUrl
````
Second:
````
concept AnalyticsCounts

purpose count successful lookups per short URL
principle every successful translation from a short URL to its target increments that short URL’s counter
state

a set of Counters with

shortUrl String

count Number
actions

init (shortUrl: String)
effect: creates a counter for shortUrl at 0 (idempotent OK)

increment (shortUrl: String)
effect: adds 1 to the counter for shortUrl

get (shortUrl: String): (count: Number)
effect: returns the counter value
````
Third:
````
concept AnalyticsDelivery [User]

purpose deliver analytics privately to a specific user
principle analytics results are only delivered to the intended user
state (none required)
actions

deliverCount (user: User, shortUrl: String, count: Number)
effect: shows count for shortUrl only to user
````

Authentication: 
```
concept UserAuth

purpose identify the current user (needed for access control)
actions

getCurrentUser (): (user: User)
effect: returns the authenticated user making the request
```


### 2. Syncs
When a shortening is created:
```
sync onRegister
when
  Request.shortenUrl (targetUrl, shortUrlBase)
  UrlShortening.register (): (shortUrl)
  UserAuth.getCurrentUser (): (user)
then
  ShorteningOwnership.assignOwner (shortUrl, user)
  AnalyticsCounts.init (shortUrl)
```
When a shortening is translated:
```
sync onLookup
when UrlShortening.lookup (shortUrl): (targetUrl)
then AnalyticsCounts.increment (shortUrl)
```
When a user views analytics:
```
sync viewAnalytics
when
  Request.getAnalytics (shortUrl)
  UserAuth.getCurrentUser (): (user)
  ShorteningOwnership.getOwner (shortUrl): (owner)
  AnalyticsCounts.get (shortUrl): (count)
where user == owner
then AnalyticsDelivery.deliverCount (user, shortUrl, count)
```
### 3. Modularity
1. Allow an alternate request path that bypasses NonceGeneration. Add request: Request.customShorten (targetUrl, desiredSuffix, shortUrlBase). Ownership + analytics still handled by onRegister.
```
sync registerCustom
when Request.customShorten (targetUrl, desiredSuffix, shortUrlBase)
then UrlShortening.register (shortUrlSuffix: desiredSuffix, shortUrlBase, targetUrl)
```

2. Don’t change NonceGeneration, instead add a parallel generator concept and a policy sync. The tradeoff is better memorability vs. lower entropy per character.
WordNonceGeneration [Context] with dictionary, k, and generate(context):(nonce) that returns a unique phrase per context.
```
sync generateNonce
when Request.shortenUrl (shortUrlBase)
then WordNonceGeneration.generate (context: shortUrlBase)
```

3. The issue is grouping purely by target could accidentally leak traffic if two users shorten the same target. A safer approach would be to group by (owner, targetUrl)
```
OwnerTargetCounts [User]

state: tuples (owner, targetUrl, count)
actions: initIfAbsent(owner, targetUrl), increment(owner, targetUrl), get(owner, targetUrl):(count)

sync countByOwnerTarget
when
  UrlShortening.lookup (shortUrl): (targetUrl)
  ShorteningOwnership.getOwner (shortUrl): (owner)
then
  OwnerTargetCounts.initIfAbsent (owner, targetUrl)
  OwnerTargetCounts.increment (owner, targetUrl)
```

4. The way to approach this would be to add concept NonceQuality with action acceptable (nonce: String): (ok: Flag) using rules and add sync that regenerates until acceptable:
````
sync qualityGate
when
  Request.shortenUrl (targetUrl, shortUrlBase)
  NonceGeneration.generate (context: shortUrlBase): (nonce)
  NonceQuality.acceptable (nonce): (ok)
where ok
then
  UrlShortening.register (shortUrlSuffix: nonce, shortUrlBase, targetUrl)
````
If ok is false, this sync doesn’t fire; a separate sync could loop (requesting another generate) or you can phrase NonceGeneration.generate to always be called by a “nonce picker” concept that enforces the policy.


5. This would be harder to implement because we use UserAuth to check users and ownership. I think this would be undesirable because it creates security issues and could lead to potential data leaks. This also may lead to some users not making accounts, which froma  product perspective isn't what we want, account creation should be pushed as it helps the ShorteningOwnership concept.





