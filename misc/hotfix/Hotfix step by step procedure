# Hotfix & Cherry-Pick Procedure

When an urgent fix needs to be deployed to production without waiting for the full release cycle, follow this process:

## 1. Identify the Base Branch
- Find the **last branch deployed to production**.
- **Important:** Check for short-lived branches (previous hotfixes) that might have been deployed after the main release branch. These must be included in your base.

## 2. Create a Hotfix Branch
- Create a new branch from the production base commit.
- Follow naming conventions for the project, for instance:
  - `hotfix/<ticket-id>-<short-description>`
  - `release-fix/<commit-hash>`
  - `hotfix/from-<commit-hash>-fix`
- Consistency matters—ensure your team agrees on a prefix like `hotfix/`.

## 3. Apply the Fix
- **Option A:** Commit the fix directly on the hotfix branch.
- **Option B:** Cherry-pick the required commit(s) from another branch (e.g., `develop`).

## 4. Create a Pull Request (PR)
- Open a PR for visibility and tracking (even if it cannot be merged automatically).
- This PR is mainly for **Product/QA awareness**, not for merging into the mainline.
- The PR can also be used for merging the fix into develop - but then beaware not to delete the hotfix branch (in cases when this branch is the one to be deployed to production).

## 5. Deploy the Hotfix
- The hotfix branch can be deployed **directly to any environment**, including production.
- **Note:** This branch may not have gone through the full CI/CD pipeline, so validate on a lower environment whenever possible.
- The idea is that the change is small and that it is applied on a stable tested commit. This is an emergency case, so that the procedure is not as stable as in case of ordinary deployments.

## 6. Sync Back to Mainline
- Ensure the same changes exist on `develop` (or your main development branch).
- Options:
  - **Small change:** Create a separate PR from hotfix → develop.
  - **Alternative:** Cherry-pick the hotfix commit(s) into `develop`.

---

### ⚠️ Good Practices
- Keep hotfix branches **short-lived**; delete after merging back.
- Document why the hotfix was needed and what was changed.
- Avoid stacking multiple hotfixes without merging back to `develop`—it complicates history.
- Always verify that production is based on a **tested state**; hotfix assumes minimal, isolated changes.
``
