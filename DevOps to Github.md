# migrate-ado-to-ghe
Migrate Azure DevOps to Github Enterprise
# Migrate Azure DevOps to Github Enterprise

Good to read [click here](https://github.com/github/gh-gei#readme)

## Objectives:
- Migrate a single repository from Azure DevOps to Github Enterprise (GHE)
- Migrate multiple repositories in batch from Azure DevOps to Github Enterprise (GHE)
- Migrate ADO 'organization(s)', which contains multiple projects, repos, branches, tags, commits, code review comments,  and pipelines, to GitHub Enterprise (GHE)
-----------------

## Migration Approach:
- Migrate repositories from ADO to GH [click here](https://github.com/e2eSolutionArchitect/migrate-ado-to-ghe/blob/main/README.md#check-migration-instructions-here)
- Migrate Azure Pipelines to GitHub Actions
- Migrate remaining assets (boards, artifacts) from ADO to GitHub
  
-----------------
# Preparation:
- Download Github CLI [click here](https://cli.github.com/)
- Check GitHub CLI installation instructions [click here](https://github.com/cli/cli#readme)
- Install GitHub Enterprise Importer (GEI) extension and ado2gh extension
- Generate Personal Access Token (PAT) from source and target repositories. GitHub > Settings > Profile > Personal Access Token
- If migrating from ADO to GHE then source PAT = ADO and target PAT = GHE
- Set the privileges appropriately while creating the token. check required access [here](https://docs.github.com/en/migrations/using-github-enterprise-importer/preparing-to-migrate-with-github-enterprise-importer/managing-access-for-github-enterprise-importer#required-roles-for-github)
- Set environment variables  'ADO_PAT' (source PAT), 'GH_PAT' (target PAT)
- If migrating ADO to GitHub then ADO_PAT & GH_PAT
- These two variables can be passed with 'gh gei ' command but the best practice is to set as environment as a secure approach
- **IMPORTANT** make the ADO source repos READ ONLY to avoid any new change during migration
  - Select ADO repository > Settings > Security tab > Select the repos in the list and set 'Deny' in all drop-downs.
- Ensure the GitHub app [Azure pipeline](https://github.com/e2eSolutionArchitect/migrate-ado-to-ghe/blob/main/install-azure-pipeline-github-apps.md) is installed in github account. It is required to migrate ADO pipelines to GitHub.

## Analyze migration
Use the [migration analyzer tool](https://github.com/e2eSolutionArchitect/migrate-ado-to-ghe/blob/main/migration-analyzer.md) to estimate and plan for the migration. 

## Important:
- Service hooks are not migrated from ADO, it needs to be recreated separately in GH.
- Migration timing is largely based on the number of pull requests in a repository.
- GitHub Enterprise Importer does not attempt to migrate repository permissions from ADO.
- Approach separately. 1. Migrate repo from ADO to GH, 2. Migrate Azure Pipelines to GitHub Actions, 3. Migrate remaining assets (boards, artifacts) from ADO to GH


## Steps to create Personal Access Token (PAT) 
- ## Azure DevOps
 - User settings (top right corner second last icon) > Personal Access Tokens
 - Create a new token. Important: If you plan to migrate multiple org then select 'All accessible organizations' in the field of 'Organization'
 - You will need three scopes. Work Item Read, Code Read, Identity Read. Note: click over 'show all scope' link to get the Identity Read option.
 - Create the token
  
- ## GitHub
 - Profile > Settings > Developer Settings > Personal access tokens

## Azure DevOps to GitHub Usage
Create Personal Access Tokens with access to the Azure DevOps org, and the GitHub org (for more details on scopes needed refer to our official documentation).

Set the ADO_PAT and GH_PAT environment variables.

Run the generate-script command to generate a migration script.

gh ado2gh generate-script --ado-org ORGNAME --github-org ORGNAME --all

The previous command will have created a migrate.ps1 PowerShell script. Review the steps in the generated script and tweak if necessary.

The migrate.ps1 script requires PowerShell to run. If not already installed see the install instructions to install PowerShell on Windows, Linux, or Mac. Then run the script.

Refer to the official documentation for more details.

## Tips
- check the available commands for gh gei by running 'gh gei --help' or 'gh gei migrate-repo --help'

### Install GH extension
#### Run the below commands in GitHub CLI
```
# Check the installed extension list
gh extension list
```
#### The extension for GEI (GitHub Enterprise Importer) is located [here](https://github.com/github/gh-gei)
Please check the installation instruction for the extension [here](https://github.com/github/gh-gei#readme)

```
# To install gei
gh extension install github/gh-gei

# To install ado2gh
gh extension install github/gh-ado2gh

# verify installation
gh extension list
```

## Set Environment variables
```
# for LINUX system. Migrating ADO to GHE
export ADO_PAT=##################
export GH_PAT=##################

# verify
echo $ADO_PAT
echo $GH_PAT
```
## Check migration instructions here
- [Single repo migration](https://github.com/e2eSolutionArchitect/migrate-ado-to-ghe/blob/main/single-repo-migration.md)
- [Batch migration](https://github.com/e2eSolutionArchitect/migrate-ado-to-ghe/blob/main/batch-migration.md)

## Post Migration checklist [click here](https://github.com/e2eSolutionArchitect/migrate-ado-to-ghe/blob/main/post-migration.md)

Note: 
- There is a size limit over which importer doesn't work. migration file size > 10GB (it may change, please verify)
