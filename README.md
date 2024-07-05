# golive-send-environment-info action

Use this action to push any environment information (ex: deployment, status, configuration,...) to Golive when your workflows are changing your environments.

This is your “swiss knife” task to track your environments managed in Github.

## What is Apwide Golive for Jira?

Apwide Golive is the game-changing solution for comprehensive test environment management.

Seamlessly integrated with your development and release processes, Apwide Golive empowers your teams to
deliver high-quality software with speed and confidence.
Apwide Golive provides a centralized dashboard for visualizing and tracking environment usage, resource allocation, and availability right in Jira.

With integrated notifications and approvals in Jira, stakeholders stay informed and can provide quick feedback, reducing delays and accelerating the testing
process.

Learn more about Apwide Golive: https://www.apwide.com

## Inputs

(*) required

### goliveToken
Golive API Token     
### goliveUrl
Api Base Url.
Default value: `https://golive.apwide.net/api`
### goliveUsername
Golive API Username (Golive Server)
### golivePassword
Golive API Username (Golive Server)
### githubToken (*)
Github token (`${{secrets.GITHUB_TOKEN}}`)
### targetEnvironmentId
Target environment the version is deployed to
### targetEnvironmentName
Name of the environment in Apwide Golive
### targetEnvironmentAutoCreate
Create the target environment with provided information if it does not exist yet in Apwide Golive.
### targetApplicationName
Type the name of the application to select or create the target environment.
### targetApplicationId
Select an existing application to use to find/create the target environment.
### targetApplicationAutoCreate
Create the target application with provided name if it does not exist yet in Apwide Golive.
N.B. User associated to your connection must have permission to manage applications in order to automatically create new applications.
### targetCategoryName
Type the name of the category to find/create the target environment.
### targetCategoryId
Select an existing category to use to find/create the target environment.
### targetCategoryAutoCreate
Create the target category with provided name if it does not exist yet in Apwide Golive.
N.B. User associated to your connection must have permission to manage categories in order to automatically create new categories.
### deploymentVersionName
Deployed version name
### deploymentBuildNumber
Build number of the deployed version. Ex: $(Build.BuildNumber)
### deploymentDeployedDate
Date in ISO-8601 format. Ex: 2023-09-24T12:00:00Z
#### deploymentDescription
Description of the deployment (markdown is supported). Ex:
```shell
✅ **Job** #$(Build.BuildId) - $(Build.DefinitionName)
Requested by: $(Build.RequestedFor)
Branch: $(Build.SourceBranchName)
```
### deploymentAttributes
Deployment attribute values in key-value JSON format. Ex:
```json
{
    "Requested By" : "me@company.com",
    "Artefacts" : "https://binaries.company.com/download/232323",
    "Repository" : "https://github.com/"
}
```
### deploymentIssueKeys
Comma separated list of Jira issue keys that are deployed. Ex: ECOM-3412,ECOM-6783,PAY-98
### deploymentIssueKeysFromCommitHistory
Parse and retrieve the Jira issue keys found in commit history and add them to the current deployment.
This option requires you to have checked out your repository with `fetch-depth: 0` of the *actions/checkout@v4* action
### deploymentIssuesFromJql
Set the JQL query used to retrieve and add Jira tickets to the current deployment.
Ex: project = ECP AND type in (Story, Bug) AND resolution in (Fixed).
More about JQL [here](https://www.atlassian.com/software/jira/guides/jql)
### deploymentAddDoneIssuesOfJiraVersion
Add all "Done" issues fixed in the deployed Jira version to the current deployment
### deploymentNoFixVersionUpdate
Do not add the deployed Jira version to "Fix versions" of the deployed Jira issues.
### deploymentSendJiraNotification
Trigger the standard Jira notification when updating the "Fix versions" of the deployed issues.
### environmentStatusName
Type the name of the new status of the environment
### environmentStatusId
Select the new status of the environment
### environmentUrl
Update the url of the environment
### environmentAttributes
Environment attribute values in key-value JSON format. Ex:
```json
{
    "OS" : "Linux",
    "Location" : "Switzerland",
    "Owner" : "me@company.com"
}
```

## Outputs
### status
Status of the action.

## Example

### Push commit tickets and version info to existing environment by id
```yaml
uses: apwide/golive-send-environment-info@main
with:
  goliveToken: ${{ secrets.JRE_GOLIVE_TOKEN }}
  githubToken: ${{ secrets.GITHUB_TOKEN }}
  targetEnvironmentId: 488
  deploymentVersionName: ECOM-2.2
  deploymentIssueKeysFromCommitHistory: true
```

### Full example
Following example is going to:
* if target environment, application and/or category do not exist, they will be created automatically. (*target\*\*\*AutoCreate*)
* send deployment information to Golive including issue keys found in commits + overrident deploymentIssueKeys.
* if issues are linked to deployed version into Jira, they will be linked to the deployment too.
* issues will not have their fix version set with current deployed version.
* environment status will be set to *Up*
* environment url and attributes will be updated with provided information.
* no Jira notification will be sent for issue updates.

```yaml
uses: apwide/golive-send-environment-info@main
with:
  goliveToken: ${{ secrets.JRE_GOLIVE_TOKEN }}
  githubToken: ${{ secrets.GITHUB_TOKEN }}
  targetEnvironmentName: 'eCommerce Demo'
  targetEnvironmentAutoCreate: true
  targetApplicationName: 'eCommerce'
  targetApplicationAutoCreate: true
  targetCategoryName: 'Demo'
  targetCategoryAutoCreate: true
  deploymentVersionName: 'ECOM 2.3.4.45-Alpha'
  deploymentBuildNumber: '$(Build.BuildNumber)'
  deploymentDeployedDate: '2023-09-24T12:00:00Z'
  deploymentDescription: |
    ** ✅ Job #$(Build.BuildId) - $(Build.DefinitionName)**
    Requested by: $(Build.RequestedFor)
    Branch: $(Build.SourceBranchName)
  deploymentAttributes: |
    {
    "Requested By" : "me@company.com",
    "Artefacts" : "https://binaries.company.com/download/232323",
    "Repository" : "https://github.com/"
    }
  deploymentIssueKeys: 'ECOM-3412,ECOM-6783,PAY-98'
  deploymentIssueKeysFromCommitHistory: true
  deploymentIssuesFromJql: 'project = ECOM and type in (Story, Bug) AND resolution in (Fixed)'
  deploymentAddDoneIssuesOfJiraVersion: true
  deploymentNoFixVersionUpdate: false
  deploymentSendJiraNotification: false
  environmentStatusName: 'Up'
  environmentUrl: 'https://ecommerce.staging.company.com'
  environmentAttributes: |
    {
    "OS" : "Linux Ubuntu",
    "Location":"Switzerland",
    "Owner":"me@company.com"
    }
```

## Contact us

We are at your disposal if you have question or need support regarding Apwide Golive and this integration with Github: https://www.apwide.com/support-documentation/
