---
title: "Task 2 - Setup Google Cloud Shell"
menuTitle: "Setup Google Cloud Shell"
weight: 2
---

### Setup Google Cloud Shell

1. Activate Cloud Shell

    Click on the icon as shown below screenshot to activate the Cloud Shell, which should connect to the project.

    ![activateCloudShell](gcloud-shell.png)

2. Authorize Cloud Shell

    ![authorizeCloudShell](gcloud-authorize.png)

3. Copy & Paste the below command to set up the environmental variables.

    ```
    project=$(gcloud config list --format="value(core.project)")

    export region="us-central1"
    export zone="us-central1-a"
    
    gcloud config set project $project
    gcloud config set compute/region $region
    gcloud config set compute/zone $zone
    
    gcloud config list
    ```

    {{< notice note >}} Enable APIs if it prompts for.{{< /notice >}}

    > output will be similar as below

    ![envOutput](gcloud-env-output.png)

4. Validate Project

    ```
    gcloud config get project
    ```
    
    > output will be similar as below

    ![envOutput](gcloud-project.png)

5. Validate Region

    ```
    gcloud config get compute/region
    ```
    
    > output will be similar as below

    ![envOutput](gcloud-region.png)

6. Validate Zone

    ```
    gcloud config get compute/zone
    ```
    
    > output will be similar as below

    ![envOutput](gcloud-zone.png)
