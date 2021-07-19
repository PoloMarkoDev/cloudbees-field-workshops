---
title: "Cross Team Collaboration"
chapter: false
weight: 5
---

## Enable Cross Team Collaboration Notifications

We will utilize CloudBees CI CasC to enable and configure Notifications for Cross Team Collaboration.

1. Navigate to your `cloudbees-ci-config-bundle` repository in GitHub and click on the **Pull requests** link. ![PR link](pr-link.png?width=50pc) 
2. On the next screen, click on the **Cross Team Collaboration Lab: Enable Notifications** pull request and then click on the **Files changed** tab to review the requested configuration changes and scroll down to the `jenksin.yaml` file. As you can see, we are adding `notificationConfiguration` for your CloudBees CI Managed Controller. ![PR Files Changed](collab-casc-changes.png?width=50pc)
3. Once you have reviewed the changed files, click on the **Conversation** tab, scroll down and click the green **Merge pull request** button and then the **Confirm merge** button.
4. On the next screen click the **Delete branch** button.
5. Navigate to the **config-bundle-ops** job under the **template-jobs** folder on your CloudBees CI Managed Controller. After the **main** branch job completes successfully **Cross Team Collaboration** notifications will be enabled for your *managed controller*.

## Adding an event trigger

Now that we have configured CloudBees CI Notifications for our ***managed controllers*** we will add an event trigger to a Pipeline template.

1. In GitHub, navigate to the **Cross Team Collaboration: Add Event Trigger** pull request (#1) in your fork of the **pipeline-template-catalog** repository. ![Event trigger PR](event-trigger-pr.png?width=50pc)
2. Click on the  **Cross Team Collaboration: Add Event Trigger** pull request link and then click on the **Files changed** tab to see the changes that will be made to the **Maven Pipeline Template**. ![Event trigger changes](event-trigger-changes.png?width=50pc)
3. We are adding the `eventTrigger` using `jmespathQuery` and adding a new `stage` that will on run when the Pipeline is triggered by an `EventTriggerCause`, and in that `stage` we are using the `getImageBuildEventPayload` Pipeline Shared Library step to extract the event payload.
```groovy
  triggers {
    eventTrigger jmespathQuery("event=='imagePush' && name=='maven'")
  }
```
4. Once you have reviewed the changes, click back on the **Conversation** tab and then click the green **Merge pull request** button, then the **Confirm merge** button and then delete the branch.
5. Next, to ensure that we are using the updated **Maven Pipeline Template**, we will check the Pipeline Template Catalog **Import Log**. Navigate to the top-level of your CloudBees CI Managed Controller and click on **Pipeline Template Catalogs** link in the left menu and then click the **workshopCatalog** link. 
   - ***NOTE:*** *Because the **pipeline-catalog-ops** project is a Multibranch pipeline it will be triggered via a GitHub webhook on all code commits resulting in a re-import of the Pipeline Template Catalog.* ![workshop Catalog link](workshop-catalog-link.png?width=50pc) 
6. On the next screen, click the **Import Log** link to ensure the catalog was imported successfully and recently. 
7. In order to enable the event trigger on your **simple-maven-app** Pipeline job you need to run the job once - so navigate to the **main** branch job for the **simple-maven-app** Mutlibranch project and click the **Build Now** link in the left menu.
8. After the job completes, click on the **View Configuration** link in the left menu to view the updated job configuration for your **main** branch job that will show the addition of the event trigger. ![Trigger configured](trigger-configured.png?width=50pc)

## Create a Pipeline to publish an event

Now that you have an `eventTrigger` added to your **Maven Pipeline Template** we need to create a job that will publish an event that will trigger it. Each of you will create a simple Pipeline job that will publish an event to imitate the real world scenario where a new `maven` build image would be built and pushed - typically by another team on a different Managed Controller (Jenkins instance).

1. At the top level of your CloudBees CI Managed Controller click on the **New Item** link in the left navigation menu.
2. Enter an item name - say **publish-event** - then select **Pipeline** as the item type and then click the **OK** button. ![Create publish event pipeline](create-publish-event-pipeline.png?width=50pc)
3. Click on the **Pipeline** tab and then copy the following Pipeline, paste it into the **Script** text area and then click the **Save** button:
```groovy
pipeline {
    agent none
    options {
        timeout(time: 30, unit: 'MINUTES')
    }
    stages {
        stage('Publish Event') {
            steps {
                publishEvent event: jsonEvent('{"event":"imagePush","name":"maven","tag":"3.6.3-openjdk-15"}'), verbose: true
            }
        }
    }
}
```
![Publish event script](publish-event-script.png?width=50pc)
4. Click the **Build Now** link in the left menu and then view the logs to see the `Publishing event notification` `Event JSON`.  The `verbose` option on the `publishEvent` steps prints out the JSON being sent by the event in the logs. ![Build publish event log](publish-event-log.png?width=50pc)
5. Once the **publish-event** Pipeline job completes successfully you will see the **main** branch job of the **simple-maven-app** Mutlibranch project triggered.
6. Once the **main** branch job completes successfully you can see in the logs: `new build image: maven:3.6.3-openjdk-15`, as specified by the event you published above and see that the build was triggered by an **event**. ![Trigger success](triggered-by-event.png?width=50pc)

**For instructor led workshops please <a href="https://cloudbees-days.github.io/cloudbees-field-workshops/cloudbees-ci/#collab-lab-overview">return to the workshop slides</a>**
