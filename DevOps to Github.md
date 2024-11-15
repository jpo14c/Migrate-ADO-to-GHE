
# Migrate Azure DevOps to Github Enterprise

Good to read [click here](https://github.com/github/gh-gei#readme)

## Objectives:
- Migrate a single repository from Azure DevOps to Github Enterprise (GHE)
- Migrate multiple repositories in batch from Azure DevOps to Github Enterprise (GHE)
- Migrate ADO 'organization(s)', which contains multiple projects, repos, branches, tags, commits, code review comments,  and pipelines, to GitHub Enterprise (GHE)
-----------------

## Migration Approach:
- Migrate repositories from ADO to GH 
- Migrate Azure Pipelines to GitHub Actions
- Migrate remaining assets (boards, artifacts) from ADO to GitHub
- Before  migrate from Azure DevOps, you should plan out how you will run your migration. Before migrating any data, you will need to
  choose someone to run the migration. You must grant that person the necessary access for both the source and the destination of the migration.
  We also recommend you run a trial  migration first.
  
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
1. Create Personal Access Tokens with access to the Azure DevOps org, and the GitHub org (for more details on scopes needed refer to our official documentation).

2. Set the ADO_PAT and GH_PAT environment variables.
 ```
  $env:ADO_PAT="TOKEN"
  $env:GH_PAT="TOKEN"

```
3. Use the gh ado2gh --help command. 
   ```
   gh ado2gh --help
   gh ado2gh migrate-repo --help 
   gh-ado2gh migrate-repo [options]
   Options:  --ado-org <ado-org> (REQUIRED)
              --ado-team-project <ado-team-project> (REQUIRED)
              --ado-repo <ado-repo> (REQUIRED)
              --github-org <github-org> (REQUIRED)
              --github-repo <github-repo> (REQUIRED)
              --target-repo-visibility <internal|private|public>
              --ado-pat <ado-pat>
              --github-pat <github-pat>
              
  ```  
-----------------   
5. Run the generate-script command to generate a migration script.
    ### gh ado2gh generate-script --ado-org ORGNAME --github-org ORGNAME --all

6. The previous command will have created a migrate.ps1 PowerShell script. Review the steps in the generated script and tweak if necessary.

7. The migrate.ps1 script requires PowerShell to run. If not already installed see the install instructions to install PowerShell on Windows, Linux, or Mac. Then run the script.

Refer to the [official documentation](https://docs.github.com/en/migrations/using-github-enterprise-importer/migrating-from-azure-devops-to-github-enterprise-cloud/migrating-repositories-from-azure-devops-to-github-enterprise-cloud) for more details.

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


Note: 
- There is a size limit over which importer doesn't work. migration file size > 10GB (it may change, please verify)

  ## Generate script for bulk migration
```
### Github to Github
gh gei generate-script --github-source-org <github-source-org-name> \
--github-target-org <github-target-org-name> \
--all --output migrate.sh

### ADO to Github
gh ado2gh generate-script --ado-org <ado-source-org-name> \
--github-org <github-target-org-name> \
--all --output migrate.sh
```

- The above 'repos-only' will only consider migration for repositories. 
- The above command will generate migrate.sh as output file. If using PowerShell then rename the file to '.ps1' e.g, migrate_script.ps1 
- Now simply run this migrate_script.sh in the terminal or command line interface (CLI). It will migrate every repo to the target org in GitHub.
- If 'ado-org' is not mentioned then it will consider every org the PAT( Personal Access Token) has access to
- Similarly, if 'repos-only' is not mentioned then it will consider every team project and repos the PAT( Personal Access Token) has access to

```
gh gei generate-script --github-org <github-target-org-name> \
--output migrate.sh 
```

### Run the script
```
./migrate.sh
```



## WAIT
Have you received the below warning while generating the migration script?
**[WARNING] CANNOT FIND GITHUB APP SERVICE CONNECTION IN ADO ORGANIZATION: e2esainstructor. You must install the Pipelines app in GitHub and connect it to any Team Project in this ADO Org first.**

### If 'github-org' or 'ado-org' is not mentioned then it will consider every org the PAT( Personal Access Token) has access to
```
gh ado2gh generate-script  --github-org <github-target-org-name> --all --output migrate.sh

gh gei generate-script --github-source-org <github-target-org-name> --all --output migrate.sh
```

## For single org migration specify target org

```
gh ado2gh generate-script --ado-org <ado-source-org-name> --github-org <github-target-org-name> --all --output migrate.sh
gh gei generate-script --github-source-org <github-source-org-name>  --github-target-org <github-target-org-name> --output migrate_script.sh 
```




