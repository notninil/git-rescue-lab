# WORKFLOW.md

## Task 1 — Bisect Finding

Commit c99fb4209e6fb6e5ed2789893fdb2f893d61d6c6 introduced the regression:
it changed the BULK20 discount condition from `items.length >= 5` to
`items.length > 5`, so an order of exactly 5 items no longer qualifies
for the 20% discount.