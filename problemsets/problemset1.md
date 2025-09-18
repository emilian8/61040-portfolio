# Problem Set 1

## Exercise 1
### 1️. Invariants
- **Aggregation/Count invariant**: For every `Request`, the remaining `count` must equal the original requested count **minus** the sum of all associated `Purchase.count`. This is more critical because it directly ensures inventory correctness (no overselling or negative counts).
  The action most affected is purchase. It preserves the invariant by requiring that the request has at least `count` available and by decrementing the request count exactly by the purchased amount.
- **Request–Purchase relation invariant**: Every `Purchase` must correspond to an existing `Request` for the same `Item` inside the same `Registry`.
### 2. Fixing an action
**Problematic action**: `removeItem`, if an item has existing `Purchases` and the owner removes the `Request`, the link between purchases and their request is lost, violating the Request–Purchase relation and the count accounting.

**Fix**: Add a precondition like requires no purchases exist for this item or instead of deletion, mark the request as inactive while preserving its purchase history.


### 3. Inferring behavior
The specs do **not forbid reopening** a registry: `close` only requires active = true and `open` only requires active = false.  
- So a registry **can be opened and closed repeatedly**. We could allow this if the recipient wants to pause the registry and then reopen it.

### 4. Registry deletion
The lack of a delete action likely doesn’t matter much because closed registries can simply remain inactive, and a record of old registries are often wanted for data or memories. Although, the flexibility would be nice in case the user for whatever reason does want to delete a registry.

### 5. Queries
- **Owner query**: `getPurchasedSummary(registry)` : list each item with total purchased count and purchasers.
- **Giver query**: `getAvailableItems(registry)` : list items with remaining counts available for purchase.


### 6. Hiding purchases
Add a flag on each registry or request, creating a new action: `setHidePurchases(registry: Registry, flag: Flag)` so that when `hidePurchases` is true, it does not show the details of a purchase to the owner, keeping the suprise element.

### 7. Generic types
`User` and `Item` are generic parameters so that the concept can be reused in different contexts and integrated with external systems which provides benefits like `Item` can map to SKU codes or product IDs, which are unique. On the other hand names, descriptions, and prices can change over time, leading to ambiguity and incorrect matches.

## Exercise 2
### 1. Concept state

```text
concept PasswordAuthentication
purpose limit access to known users
principle after a user registers with a username and a password,
  they can authenticate with that same username and password
  and be treated each time as the same user
state
  a set of Users with
    username: String
    password: String
    confirmed: Flag
  a set of PendingConfirmations with
    username: String
    token: String
```
### 2. Actions
```
actions
  register(username: String, password: String): (user: User, token: String)
    requires no existing User with this username
    effects
      create User u:
        u.username = username
        u.password = password
        u.confirmed = false
      create PendingConfirmations pc:
        pc.username = username
        pc.token = SecretRandom()
      return user = u, token = pc.token

  confirm(username: String, token: String)
    requires exists PendingConfirmations pc with
               pc.username = username and pc.token = token
             and exists User u with u.username = username and u.confirmed = false
    effects
      set u.confirmed = true
      delete pc

  authenticate(username: String, password: String): (user: User)
    requires exists User u with u.username = username
             and u.confirmed = true
             and password = u.password
    effects
      return user = u

```
### 3. Essential Invariant and Preservation

Invariant: Username uniqueness & verifiable secret
For all Users u1 != u2, u1.username != u2.username; and for each u, authentication does not succeed until u.confirmed = true

Preservation:
register enforces uniqueness by requiring no existing user with the username.
confirm changes confirmed to true if successful
authenticate does not mutate state and only succeeds if the password check passes and the user is confirmed.

### 4. Extension
Previous answers were modified to include the feature to require registration be confirmed by email.



## Exercise 3

### PersonalAccessToken 

```text
concept PersonalAccessToken [User, Scope]
purpose
  allow a user to create and manage revocable, scoped credentials
  that can be used to authenticate for Git-over-HTTPS and API access
  without using the account password
principle
  a user can create one or more tokens, each with selected permission scopes and optional expiration;
  each token is displayed once on creation and stored only as a hash;
  the user (or GitHub automatically) may revoke a token at any time;
  an application or Git client presents a valid token to authenticate as the token’s owner for permitted operations
state
  a set of Users

  a set of Tokens with
    owner: User
    secretHash: Bytes 
    scopes: Set<Scope>     
    expiresAt: Time? 
    revoked: Flag
    lastUsedAt: Time?
actions
  createToken(owner: User, scopes: Set<Scope>, expiresAt?: Time): (token: Token, secret: String)
    requires owner exists
    effects
      create Token t with owner = owner, scopes = scopes, expiresAt = expiresAt,
            revoked = false, lastUsedAt = null
      generate random secret s
      set t.secretHash = Hash(s)
      return (token = t, secret = s)      // secret shown once to the creator

  revokeToken(token: Token)
    requires token exists and token.revoked = false
    effects set token.revoked = true

  authenticateWithToken(ownername: String, secret: String): (user: User)
    requires exists Token t with t.owner.username = ownername
             and t.revoked = false
             and (t.expiresAt is null or now < t.expiresAt)
             and Hash(secret) = t.secretHash
             and required scopes ⊆ t.scopes
    effects set t.lastUsedAt = now; return user = t.owner

```
### How PersonalAccessToken differs from PasswordAuthentication

| Aspect | **PasswordAuthentication** | **PersonalAccessToken (classic)** |
|--------|-----------------------------|-------------------------------------|
| **Main purpose** | Identify a user who knows a single long-term secret so they can sign in interactively. | Provide many independently managed, revocable credentials that can be used by programs or Git clients instead of the account password. |
| **Credentials** | One password per user. | Multiple opaque tokens per user; each token has its own secret. |
| **Permission model** | Password grants full account access. | Each token carries a set of scope limiting what actions it can authorize. |
| **Lifecycle management** | Password can be changed; changing it revokes all old access. | Each token can have its own optional expiration date, can be revoked without affecting other tokens, and may be auto-revoked if leaked or unused. |
| **Typical use** | Human interactive login to a web or app session. | Non-interactive, programmatic access: API calls, Git over HTTPS, CI/CD scripts, personal automation. |

---

**Differences**

- **Modularity**: PersonalAccessToken supports many independent credentials per user. PasswordAuthentication assumes one secret per user.
- **Permissions**: Tokens have scopes, so each token can be limited to specific resources or actions. Passwords have all-or-nothing access.
- **Revokes**: A single token can be revoked or can expire without disturbing other tokens or the main account password, enabling safer automation and faster response to leaks.


---

**Github Improvements**

GitHub’s current advice to “treat your tokens like passwords” can be misleading.  
It should be changed to be more clear, like “Personal access tokens are not passwords. They are independent, scoped, and revocable credentials intended for API and Git use. You can create multiple tokens, limit their permissions, set expirations, and revoke them without changing your main account password.”


# Exercise 4 — Defining Familiar Concepts

Subtleties at end

## 1. URLShortener

```
concept URLShortener [Owner, Url]
purpose
  let people create short links that point to longer URLs,
  either with a custom ending or one made automatically
principle
  someone gives a long URL and either picks a short ending or lets the system make one;
  the system saves that short link so anyone who visits it is sent to the long URL;
  the owner can change where it points or turn it off later
state
  a set of Links with
    owner: Owner
    suffix: String
    destination: Url
    active: Flag

actions
  createAuto(owner: Owner, destination: Url): (link: Link)
    requires true
    effects make a new Link with a fresh random suffix, active = true

  createCustom(owner: Owner, destination: Url, suffix: String): (link: Link)
    requires no active Link has this suffix
    effects make a new Link with the chosen suffix, active = true

  updateDestination(link: Link, destination: Url)
    requires link exists and link.active = true and caller = link.owner
    effects set link.destination = destination

  deactivate(link: Link)
    requires link exists and caller = link.owner and link.active = true
    effects set link.active = false

  resolve(suffix: String): (destination: Url)
    requires exists Link l with l.suffix = suffix and l.active = true
    effects return l.destination
```

## 2. Conference Room Booking
```
concept ConferenceRoomBooking [User, Room]
purpose
  let people reserve rooms for meetings without double-booking
principle
  a user picks a room and a time range;
  if that room is free, the booking is saved;
  the user can change or cancel it until the meeting starts
state
  a set of Bookings with
    room: Room
    organizer: User
    start: Time
    end: Time
    active: Flag

actions
  createBooking(organizer: User, room: Room, start: Time, end: Time): (booking: Booking)
    requires start < end and no active Booking in that room overlaps this time
    effects create a new active Booking

  modifyBooking(booking: Booking, newStart: Time, newEnd: Time)
    requires booking exists and booking.active = true and caller = booking.organizer
            and newStart < newEnd and no active overlapping Booking in the same room
    effects set booking.start = newStart; set booking.end = newEnd

  cancelBooking(booking: Booking)
    requires booking exists and booking.active = true and caller = booking.organizer
    effects set booking.active = false
```
## 3. Billable Hours Tracking
```
concept BillableHoursTracking [Employee, Project]
purpose
  keep track of time employees spend on projects so it can be billed later
principle
  an employee starts a work session by picking a project and writing what they are doing;
  when finished they end the session to record the stop time;
  if they forget, the session can be closed or fixed later
state
  a set of Sessions with
    worker: Employee
    project: Project
    description: String
    start: Time
    end: Time?
    active: Flag

actions
  startSession(worker: Employee, project: Project, description: String): (session: Session)
    requires worker has no other active Session
    effects create Session with start = now, end = null, active = true

  endSession(session: Session)
    requires session exists and session.active = true and caller = session.worker
    effects set session.end = now; set session.active = false

  fixSession(session: Session, newStart?: Time, newEnd?: Time)
    requires session exists and caller = session.worker
    effects update start and/or end as given; if end is set, set active = false
  autoClose(maxDuration: Duration)
    requires maxDuration > 0
    effects for each open session longer than maxDuration:
              set end = start + maxDuration; set active = false
```

### Subtleties
1. Keep each suffix unique among active links. Analytics or traffic counts can be a separate concept.
2. The key rule is no two active bookings can overlap for the same room. We skip extras like catering or recurring meetings to keep it simple.
3. Each worker can have only one open session at a time. The autoClose action handles forgotten stop times. Things like billing rates or invoices can live in other concepts.
