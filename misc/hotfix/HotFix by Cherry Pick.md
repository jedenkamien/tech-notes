## Hot Fix Using Cherry Pick

### Purpose

The purpose of this approach is to ensure my changes are applied both to the develop branch through the normal flow, and to production. I could manually apply the changes twice to both environments, or I could use the cherry pick option, which is especially useful when dealing with large changesets (although this might not occur frequently with hot fixes).

### Process
#### For Normal Development Flow
To ensure changes go through the develop branch via the normal process - which is important because then they will pass through all environment stages, generate logs, and most importantly, trigger the full pipeline with e2e tests to verify everything works correctly - simply create a standard new branch from the latest develop and add all changes there.

#### For Production Fix
Additionally, create a new branch from the last changeset of develop that is already in production (this can be done through ADO or VS). Name it appropriately, for example ADO suggests a name like "branch-from-asdfasdfsa-and-add-fix".

In the normal PR, click the Cherry Pick option and specify that you want to cherry pick the changes from this PR to the new -fix branch. This will create a new PR containing only my changes that reside in the new -fix branch.

Both PRs should be approved.

### Deployment
Selecting the changeset to be deployed to production is a straightforward option in ADO (you run the pipeline and specify the branch from which it should be triggered and the environments to which it should be deployed).
