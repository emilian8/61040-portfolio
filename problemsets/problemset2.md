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


## Extending The Design
