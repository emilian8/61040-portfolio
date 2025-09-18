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



