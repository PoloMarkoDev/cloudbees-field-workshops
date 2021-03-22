---
title: "Pipeline Template Catalogs"
chapter: false
weight: 1
---

Managing Pipeline Template Catalogs across a large number of ***managed controllers*** using the graphical user interface (GUI) is time consuming and prone to human error due to the repetitive nature of the task.

Using the provided command-line interface (CLI) allows for automated management of Pipeline Template Catalogs across multiple ***managed controllers***, which reduces efforts and ensures consistency across all development teams.


## Import Pipeline Template Catalog
This lab will explore how to manage CloudBees CI Pipeline Template Catalogs with the CloudBees CI CLI. 

1. Navigate to the top-level of CloudBees CI Operations Center - **Dashboard** - and click on the link for your Managed Controller (it will have the same names as your workshop GitHub Organization). ![Managed Controller link](managed-controller-link.png?width=60pc)
2. At the top-level of your CloudBees CI Managed Controller click on **New Item** in the left menu. ![New Item](create-new-item.png?width=60pc)
3. Enter ***pipeline-catalog-ops*** as the **item name**, select **Multibranch Pipeline** as the item type and the click the **OK** button. ![import-catalog Pipeline](create-pipeline-item.png?width=60pc)
4. Under **Branch Sources** click on **Add source** and select ***GitHub*** ![Branch Sources Configuration](add-source-github.png?width=40pc)
5. Under the **GitHub** branch source enter the following:
   - Select ***77562/\*\*\*\*\*\* (CloudBees CI Workshop GitHub App credential)*** for the **Credentials** value. 
   - Enter the URL for your copy of the `pipeline-template-catalog` as the value for the **Repository HTTPS URL** - ***https:\//github.com/{YOUR_GITHUB_ORGANIZATION}/pipeline-template-catalog.git***
     - TIP: If you navigate to your GitHub `pipeline-template-catalog` repository, and click on the **Code** button, you can then click on the *clipboard* icon to copy the Git URL for your repository. ![Copy Repo Git URL](copy-repo-url.png?width=40pc)
   - The rest of the default values are sufficient so click the **Validate** button and then click the **Save** button. ![Branch Sources Configuration](branch-source-config.png?width=50pc)
   - The `Jenkinsfile` in your `pipeline-template-catalog` repository is configured to use the CloudBees CI CLI command `cli pipeline-template-catalogs --put < create-pipeline-template-catalog.json` to create a **Pipeline Template Catalog** on your Managed Controller from the `create-pipeline-template-catalog.json` file in your `pipeline-template-catalog` repository as seen below:
     ```json
     [
       {
         "scm":{ 
           "$class":"GitHubSCMSource",
           "credentialsId":"cloudbees-ci-workshop-github-app",
           "repoOwner":"bee-cd",
           "repository":"pipeline-template-catalog"
         },
         "branchOrTag":"master",
         "updateInterval":"15m"
       }
     ]
     ```
6. A Pipeline job will be created for the `master` branch of your `pipeline-template-catalog` repository and once the job is complete you should see the following success message in the build logs: ![Catalog Imported](catalog-imported.png?width=50pc)
7. Navigate to the top-level of your Managed Controller and then click on the **Pipeline Template Catalogs** link in the menu on the left. ![Pipeline Template Catalogs link](catalog-link.png?width=40pc)
8. On the **Pipeline Template Catalogs** page ensure that the **workshopCatalog** catalog's **Status** is ***Healthy*** and then click on the **workshopCatalog** link. <p>![workshopCatalog link](workshopcatalog-link.png?width=50pc)
9.  On the **CloudBees CI Workshop Template Catalog** screen you will see the following templates listed: ![Template List](workshop-template-list.png?width=50pc)

## Enforce the Use of Templates at the Folder Level
The [CloudBees CI Folders Plus plugin](https://docs.cloudbees.com/docs/cloudbees-ci/latest/cloud-secure-guide/folders-plus) includes the ability to restrict the type of items/jobs allowed to be created in a folder. When this capability is used with [CloudBees CI RBAC](https://docs.beescloud.com/docs/cloudbees-ci/latest/cloud-secure-guide/rbac) you can easily enforce the use of approved (and tested) Pipeline templates across all your CloudBees CI users.

1. Navigate back to the top-level of your CloudBees CI Managed Controller (Jenkins instance) and click on **New Item** in the left menu.
2. For the **item name** enter ***template-jobs***, select **Folder** (be sure to select **Folder** and not **Folder Template**) as the item type and then click the **OK** button. ![Restricted Folder Item](new-folder-click.png?width=50pc)
3. Scroll to the bottom of the folder configuration and click on **Restrict the kind of children in this folder** - a [CloudBees Folders Plus](https://docs.cloudbees.com/docs/cloudbees-core/latest/cloud-secure-guide/folders-plus) feature - and then select **CloudBees CI Configuration Bundle**, **Maven Pipeline Template** and **Pipeline Policies GitOps** (the template jobs we will be using throughout the rest of the workshop) - and then hit the **Save** button. ![Restricted Folder Items](restricted-items-check.png?width=40pc)
4. Now when you click on **New Item** in the **template-jobs** folder you will be restricted to the **CloudBees CI Configuration Bundle**, **Maven Pipeline Template** and **Pipeline Policies GitOps** item types. This reduces confusion of what job types to use and enforces the use of approved templates. ![Restricted Folder New Item](restricted-folder-new-item.png?width=30pc)

**For instructor led workshops please <a href="https://cloudbees-days.github.io/cloudbees-field-workshops/cloudbees-ci/#pipeline-template-catalog-overview">return to the workshop slides</a>**   
