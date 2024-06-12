# Introduction
This package is creates a CodeCommit repository and a single CodePipeline. Both CodeCommit and CodePipeline are deployed in a single account. The CloudFormation is deployed to a second account.

The goal is to use this as a template where there are pipelines for each environment connecting to the repository.

![Diagram of Repo, Piplelines and Templates](../diagrams/Repo-to-Pipe-Wow.png)

The process requires some central resources in the repo account and then roles & policies in the local accounts.

![Architecture Diagram](../diagrams/CICD-resources-Wow.png)

The pipelines will be configured to trigger off a branch in the repository. This allows you to create and test your IaC in _dev_, then issue a Pull Request and merge into subsequent branches, e.g. _test_, _preprod_, and _prod_. 

# Templates
Following are the templates and the order in which to deploy them.

1. CodeCommit.yaml - This creates a basic CodeCommit repo. The default branch is _main_. You may want to create a _dev_ branch, and any others you want.
1. Repo test files - Once the repo is built, I've got a couple of trivial files to populate it.
    * SSM-test.yaml - This creates a Security Group and has some parameters
    * config-template-dev.json - Included to show use of a template file. This should be created in a **config-files** subdirectory of the repo.
1. CodePipeline-Global.yaml - This creates a KMS key, a shared S3 bucket for pipeline artefacts, plus a role and policy for CodePipeline.
1. CodePipeline-Local.yaml - This needs to be deployed to every account that will be receiving CloudFormation templates. It's the policies and roles to allow CloudFormation to run and cross account access to the artefact bucket.
1. CodePipeline-template.yaml - Finally, a simple template to create a pipeline for the sample file. There should be a separate pipeline for each environment of an application.
