# Open-Docs-Microsite-Template

Open-Docs-Microsite-Template is a docs-as-code implementation for Axway documentation. It is built using the [Hugo](https://gohugo.io/) static site generator with the [Google Docsy](https://github.com/google/docsy) theme. The project comes with a Jenkins pipeline configuration which is ready to be used on the designated Jenkins server (https://apigw-builder.lab.dubl.axway.int/view/OpenDocsBuilds/) internal to Axway. The setup also hosts previews of work in progress builds of release branches and pull requests. Please contact the "beaker" team which is based in Dublin.

This repository contains all files for building and deploying a **microsite** in the Axway-Open-Docs ecosystem.

## Create your own microsite using this repository

This section details how to create your own microsite in the Axway-Open-Docs ecosystem. For an overall view of the microsite architecture, see [Architecture for docs-as-code at scale](https://techweb.axway.com/confluence/display/RDAPI/Architecture+for+docs+as+code+at+scale).

### Before you start

You must have the following installed in your development environment:

* Git client
* Hugo
* Node.js

See [Set up and work locally](https://confluence.axway.com/display/RIE/Set+up+and+work+locally) for information on recommended versions of these tools and for tips on installing them in a WSL environment.

The following steps assume that you already have a GitHub account in the Axway org and that you have the permissions to create new projects in that org.

### Create a new microsite repository in Axway org

1. Go to [Axway org on GitHub](https://github.com/Axway) and click **New**.
    ![Create new repo](/static/Images/axway_github_new.png)
2. At the top of the page click **Import a repository**.
    ![Import repo](/static/Images/axway_github_import.png)
3. On the next screen, enter the clone URL of this repository and the name for your new microsite.

    * To find the clone URL of this repository, click the drop-down arrow on the Code button above and copy the HTTPS URL to your clipboard.
    * Enter a name following the naming convention `MYPROJECT`, for example `platform-management`. Note that when the Open Docs project was implemented, `MYPROJECT-open-docs` was used as the previous naming convention. You will see this naming convention used in older projects.

    ![Import details](/static/Images/axway_github_import_details.png)

### Clone the new microsite repository to your local environment

Clone your new repository:

```
cd ~
git clone git@github.com:Axway/MYPROJECT.git
```

After running these commands, you will have a local copy of the repository in the following location:

```
/home/YOUR-UNIX-USERNAME/MYPROJECT
```

### Build the site locally

Run the `build.sh` command in your site root:

```
cd ~/MYPROJECT/
./build.sh
```

The `build.sh` script performs the following:

* Adds the `docsy` theme Git submodule
* Adds the `axway-open-docs-common` Git submodule
* Installs the npm packages required by Docsy
* Runs the `hugo server` command

The website is now available locally at `http://localhost:1313/`.

When this is complete, test the **Edit on GitHub** links on your microsite and verify that they link to the Github repo for your microsite.

### Customize the content for your project

The project contains placeholder documentation content in the folder `/content/en/docs` and placeholder images in `/static/Images`. The placeholder content is copied from the [Docsy example project](https://example.docsy.dev/) (with some modifications) and shows the different types of content and the different types of formatting that are available for you to use when creating your own content.

You must replace or update the placeholder content as necessary with your own documentation content.

When working with the content it can be useful to read the following Docsy documentation to get an understanding of how to add content files and images, and how to change the navigation of the content using frontmatter fields in Markdown files:

* [Adding Content](https://www.docsy.dev/docs/adding-content/content/)
* [Navigation and Search](https://www.docsy.dev/docs/adding-content/navigation/)

### Customize the microsite landing page

The landing page for the microsite is a HTML page `content/en/_index.html` and uses Docsy content blocks. You must modify this page to create your own blocks and link to your own content.

### Set up publishing to Zoomin

To enable publishing of the microsite content as a new _bundle_ on the Zoomin production doc portal you must create a classification file, properties file, and zip file as detailed in [Docs-as-code on Zoomin](https://techweb.axway.com/confluence/display/RDAPI/Docs-as-code+on+Zoomin).

The upload of built html files to Zoomin is automated using Jenkins jobs and should be setup on the same Jenkins build server. Please contact "beaker" team based in Dublin.

### Remove documentation content from main Axway-Open-Docs repo (optional)

If your content was migrated from the main Axway-Open-Docs site to a microsite, you must now:

* Remove all documentation content and images from the main site
* Update the Zoomin classification file for the main site to remove the migrated content


### Customize your Git repo for your way of working

You can set up your Git repo in whatever way works best for your team and your product, however, to be accepted into the Axway-Open-Docs ecosystem you must enable the following as a minimum.

#### Configure Dependabot alerts and security updates

You must configure dependabot alerts and security updates for your microsite repository. GitHub's Dependabot creates pull requests, alerts, and sends email notifications when a dependency needs to be upgraded in your project because a dependency has introduced a security risk.

1. In your GitHub microsite project, click **Settings**.
2. Click **Security & analysis**.
3. Enable **Dependabot alerts** and **Dependabot security updates**. The button next to the setting says **Disable** when enabled.
4. Click **Save changes**.

#### Add rules to protect branches

You must add rules to protect the `master` branch. Type the branch name pattern `master` and then enable the following rules:

* Require pull request reviews before merging (at least 1 review from a technical writer or doc owner)
* Require status checks to pass before merging. The following status checks are required for pull requests:
    * **Axway/cla** - A check that verifies the contributor has signed the Axway CLA.
    * **MarkdownChecks** - A check against the `markdownlinst.json` file to verify the Markdown adheres to the rules. This is dependent on the `ciworkflow.yml` file and enabling **Actions** for the repo. When both are configured Markdown linting is performed. If the Markdown does not pass the rules specified in the linter file, that status check is unable to pass, which blocks the pull request. See [Markdown linting](#markdown-linting).
* Require linear history

![Branch protections](/static/Images/microsite_github_protections.png)

#### Markdown linting

You must use the GitHub Action that runs the Markdown linter (see `.github/workflows/ciworkflow.yml`).

Markdown linting and checks are completed when a PR is submitted after all configuration is completed. The following is required to enable MarkdownChecks.

* **markdownlint.json**: A file in each repo that specifies the Markdown linter rules.
* **ciworkflow.yml**: A workflow to run Markdown linting on the PR content against the `markdownlint.json` rules file.
* **GitHub Actions**: Click the **Actions** tab for the repo and enable workflows. This enables running the workflows saved in the `.github/workflows` folder. If you do not enable workflow actions, then the workflows will not run and the check for the MarkdownChecks will not execute.

#### Verify required status checks and workflows are executing

After you have configured your repo and you are ready to complete pull requests, you must verify that the required status checks and workflows are executing. You can verify it by the following ways:

*When a PR is submitted, but before it is merged*:

1. Create a PR and wait for the status checks to complete.
2. Make sure you see the required checks and that they have passed.

![GitHub Pull Request Status](/static/Images/github_pr_status.png)

*After a PR is completed and has been merged*:

1. Click a Closed PR.
2. Click the **Checks** menu.
3. Make sure you see the required checks.

![GitHub Pull Request Checks](/static/Images/github_pr_checks.png)

#### Merge strategy

It is best to use a squash merging strategy in public projects with external contributors as this results in a more readable Git history and cleaner change history messages at the bottom of each documentation page.

To enable squash merging, select the **Allow squash merging** checkbox and deselect the other options under **Settings > Options > Merge button**. When using this option it is best to also enable the linear history requirement on any protected branches.

You can also enable **Automatically delete head branches** to keep your branch list clean.

#### Branching strategy

You can set up any branching strategy as required by your product team.

Some common options include:

* A single production branch (`master`) for single version continuously delivered products
* A development branch (`develop`) and a production branch (`master`) for single version products delivered on a regular cycle
* A latest version production branch (`master`) and one or more previous version sustaining production branches (`762`, `753`, etc.)

#### Project maintainers and CODEOWNERS

You must add the appropriate users as maintainers of the GitHub repo under **Settings > Manage access**. Users must have write access as a minimum to review and approve pull requests. It is best to have at least two maintainers.

Next, modify the CODEOWNERS (`.github/CODEOWNERS`) file with the appropriate users. This automates who gets added as reviewers in pull requests. You can globally add all maintainers as codeowners or opt for specific owners for specific files or directories as shown in the file comments.

#### Pull request and issue templates

It is best to modify the following templates to suit your project:

* Pull request template (`.github/pull_request_template.md`) - This template is used when a contributor creates a PR on GitHub.
* Issue templates (`.github/ISSUE_TEMPLATE/documentation-issue-template.md` and `.github/ISSUE_TEMPLATE/website-issue-template.md`) - These are used when a contributor creates a GitHub issue.

### Update the microsite repo README.md

When everything is tested and you are ready to make your repo available to contributors, update the `README.md` file in the root of the microsite repo to explain to contributors what your microsite contains, how it fits into the overall Axway-Open-Docs ecosystem, and how they can contribute. You can use something similar to the README in <https://github.com/Axway/axway-open-docs>.
