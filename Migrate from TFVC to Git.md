### If you’re migrating on Microsoft Windows, use the git-tfs extension ###

Team Foundation Server (TFS), renamed Azure DevOps Server in 2019, is a set of tools developed by Microsoft which also includes Team Foundation Version Control (TFVC), a centralized version control system similar to Git.
In this document, we focus on the TFVC to Git migration.
If you migrate with git-tfs, you will retain the full history of your TFVC repository.

To follow these steps, you must use Windows and have the following tools installed:

Visual Studio Team Explorer
git-tfs, installed using Chocolatey or by downloading the binary release manually
Git

1. Create a new repository on GitHub. To avoid errors, do not initialize the new repository with README, license, or gitignore files. You can add these files after your project has been pushed to GitHub. For more information, see "Creating a new repository."

2. To confirm that Git is installed on your machine, run git --version.

The output should be similar to git version 2.40.0.

3. To confirm that git-tfs is installed on your machine, run git tfs --version.

The output should start with something similar to git-tfs version 0.32.0.0 (TFS client library 16.0.0.0 (MS)).

If you get an output similar to Unable to load DLL 'Microsoft.VisualStudio.Setup.Configuration.Native.dll': The specified module could not be found. (Exception from HRESULT: 0x8007007E), make sure that Visual Studio Team Explorer is correctly installed.

4. If you haven't already, configure Git with your name and email address by running git config --global user.name "NAME" and git config --global user.email EMAIL, replacing NAME with your name and EMAIL with your email address.

5. Run git tfs clone, passing your TFVC repository’s URL and repository path as arguments. For example, to convert the example repository from https://dev.azure.com/octocat into a Git repository stored in the /example directory, run git tfs clone --branches=all https://dev.azure.com/octocat $/example.

6. Move into the directory for the newly-created Git repository.

7. To add your GitHub repository as a remote, run git remote add origin URL, replacing URL with the URL for the GitHub repository you created earlier, such as https://github.com/octocat/example-repository.git.
   
8. Create main branch run  git branch -M main

9. To push the repository to GitHub, run git push -u origin main origin.

If your repository contains any files that are larger than GitHub's file size limit, your push may fail. Move the large files to Git LFS by running git lfs import, then try again.

