### How to Migrate from Azure DevOps Pipelines into GitHub Actions
GitHub-actions-importer  [doc](https://docs.github.com/en/actions/migrating-to-github-actions/using-github-actions-importer-to-automate-migrations/automating-migration-with-github-actions-importer <br>
Azure DevOps migrations powered by GitHub Actions Importer  [video](https://www.youtube.com/watch?v=gG-2bkmBRlI)

1. Install GitHub CLI and gh-actions-importer extension
2. To run the migration, we need two PAT tokens. One for the GitHub and another for the         Azure DevOps. GitHub token requires workflow scope to work. Azure DevOps token  requires following permissions to work:
•	Agents Pool: Read
•	Build: Read
•	Code: Read
•	Release: Read
•	Service Connections: Read
•	Task Groups: Read
•	Variable Groups: Read
3. Now we have everything set and we can configure the GH CLI extension by running configure command. ```gh actions-importer configure```<br> Answer the questions that wizard asks and use default for GitHub and Azure DevOps    base URLs. Don’t add your organization names to these.
4. Next we need to update the actions importer, even though that we just installed it. I don’t know why this needs to be done, but without it the migration will most probably produce an error. Run update with following command:  ```gh actions-importer update```


5. Now we are ready to simulate the migration and see how well this tool can handle all of our pipeline quirks. To run the simulation use Audit command with output directory.<br>
```gh actions-importer audit azure-devops --no-ssl-verify --output-dir C:\Users\jpo14c\PROJECTS\pipeline_2_github```
## Migration
Finally we can run our importer and do the actual migration. Great thing about this tool is that it will create pull request about the imported action. It won’t just magically create things into your GitHub repository. To be able to create a pull request is a powerful tool to inspect the final result, maybe create an extension and rerun the the migration. Pull request can also be reviewed by your co-workers, so you get that extra security and safety over what you are doing.
Migration is run with migrate azure-devops pipeline command. You need to pass the pipeline-id (if you want to run it just for one pipeline), GitHub target URL and directory for logs. You can find the pipeline id easily by editing a pipeline at Azure DevOps and check the URL.
```gh actions-importer migrate azure-devops pipeline --no-ssl-verify --pipeline-id (pipeline id at Azure DevOps) --target-url https://github.com/(your organization)/(your repository) --output-dir ghactionsmigrate```
