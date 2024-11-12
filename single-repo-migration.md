### Migrating single repo ADO to GitHub

```
gh ado2gh migrate-repo --ado-org e2esainstructor \
--ado-team-project <ado-src-project> --ado-repo <ado-src-repo> \
--github-org <github-target-org-name> \
--github-repo <github-target-repo-name>
```
NOTE: 'github-target-repo-name' can't be any existing repo name under 'github-target-org-name'
