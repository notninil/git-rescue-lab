# WORKFLOW.md

## Task 1 — Bisect Finding

Commit c99fb4209e6fb6e5ed2789893fdb2f893d61d6c6 introduced the regression:
it changed the BULK20 discount condition from `items.length >= 5` to
`items.length > 5`, so an order of exactly 5 items no longer qualifies
for the 20% discount.

## Branching Strategy Recommendation

For a team of 4 building this further, I'd recommend GitHub Flow: a single
main branch that's always deployable, short-lived feature branches merged
via pull request, and deploys straight from main. It has less overhead than
Git Flow (no separate develop/release/hotfix branches to maintain) while
still giving each person an isolated space to work and a review step
before code lands on main — a good fit for a small team shipping
continuously rather than managing multiple release versions.

## Fully Removing the Secret

`git rm --cached` only stops the file from being tracked going forward;
the old .env content is still reachable in commit f47fcecb and in the
repo's history/objects. To actually remove it, I'd need to rewrite every
commit that touched the file using a tool like `git filter-repo` (or the
older BFG Repo-Cleaner), then force-push and have any collaborators
re-clone, since rewriting changes commit hashes. This assignment didn't
require that step because it's destructive to shared history and overkill
for a lab with clearly fake credentials — in a real leak, the actual
secret would also need to be rotated/revoked regardless of whether history
gets scrubbed, since exposure already happened the moment it was pushed.

## Why Rewriting History Was OK in Task 2 But Not Always

Rewriting the `asdf` commit was fine because those commits existed only on
my machine at the time — no one else had pulled or built on them. Rewriting
a commit changes its hash, so if a teammate had already pulled it, their
local history would diverge from mine: their next pull or merge would
create duplicate commits or conflicts, and any work they'd built on top of
the original commit would become orphaned. The rule of thumb is: rewrite
freely before sharing, never after others have pulled.