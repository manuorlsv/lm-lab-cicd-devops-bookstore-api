# Pushing your image to container registry

In this stage we'll update your CircleCI pipeline to push to your [GCP Container Registry](https://cloud.google.com/container-registry)

### Pre-requisites

You'll need to have a container registry ready to push your docker images to. The terraform configuration covered in a [previous session](https://github.com/techreturners/lm-lab-eks-terraform-devopsupskill) provisioned your own Container Registry. You can use this to re-create your container registry.

Alternatively you can create a container registry manually using the Google Cloud console.

### Step 1 - Update config.yml

Remember back on the initial setup we utilised a Python CircleCI orb for building the Python application and running the tests. We'll now take that same approach and use the [gcp-gcr orb](https://circleci.com/developer/orbs/orb/circleci/gcp-gcr) for building the image and pushing to GCP Container Registry.

Update your `config.yml` to have the following contents:

**NOTE:** You can also see an [example of this configuration](../.circleci/gcp.example.config.yml)

```yaml
version: 2.1

orbs:
  python: circleci/python@1.5.0
  gcp-gcr: circleci/gcp-gcr@0.15.1

workflows:
  test-build-push-image:
    jobs:
      - python/test:
          args: '--dev'
          pkg-manager: pipenv
          test-tool: unittest 
      - gcp-gcr/build-and-push-image:
          image: devops-bookstore-api
          registry-url: eu.gcr.io
          tag: '1.$CIRCLE_BUILD_NUM'
          requires:
            - "python/test"
```

If we explore the changes - you can see that we continue to utilise the Python orb and introduce the new `gcp-gcr` orb.

We also introduce a new `job` called `gcp-gcr/build-and-push-image`. Let's explore the setup of that job

Firstly we define the image name we'll be pushing to `image: devops-bookstore-api` - this name will need to match the exact name of your container registry.

Then we define how the Docker image will be tagged. CircleCI provides us with some [built in environment variables](https://circleci.com/docs/2.0/env-vars/#built-in-environment-variables) such as the build number. We make use of that to specify the tag as `1.$CIRCLE_BUILD_NUM`. For example if it was build number 7 then the tag of the image would be **1.7**.

Finally we have the stage `requires` stage. By default CircleCI will run jobs in parallel. By specifying `requires` it means this stage of the build will NOT run until the `python/test` stage has completed.

**NOTE:** Don't commit and push your changes just yet until we've configured Circle with access to your GCP account. If you do accidentally commit and push, don't worry the build will just fail at this point. 

### Step 2 - Configure Circle with Google Cloud Access

Currently the [gcp-gcr orb](https://circleci.com/developer/orbs/orb/circleci/gcp-gcr) doesn't know how to access your Google cloud account.

If you browse the orb documentation you can see it mentions different configuration parameters such as the `google-project-id`, and the `gcloud-service-key`. Notice as well it references a **default** for some of the values such as `GOOGLE_PROJECT_ID` - that default means it will look for a Circle build environment variable.

Those types of *secrets* shouldn't be kept within the code so we'll make use of [storing variables in the CircleCI project](https://circleci.com/docs/2.0/env-vars/#setting-an-environment-variable-in-a-project).


On the CircleCI dashboard navigate to your new project and go into the **Project Settings**

![CircleCI projects settings](./images/circle_projectsettings.png "CircleCI project settings")

Once there click on **Environment Variables** and click **Add Environment Variable**

Add the following environment variables, entering the name exactly as shown:

| Name        | Value       |
| ----------- | ----------- |
| GCLOUD_SERVICE_KEY       | In a past session you will have created a service account and downloaded the JSON file that holds the credentials for that service account. For example your  **lm-lab-gke-gitops-devopsupskill** repository should contain a file called service-account.json. Copy the contents of that value and paste it into the text box for the environment variable.     |
| GOOGLE_PROJECT_ID      | Enter the Google project Id as the value      |

Once you have configured your **Environment Variables** it should look similar to the image below:

![CircleCI GCP Environment Variables](./images/circle_gcp_env_var.png "CircleCI GCP Environment Variables")

Now the required environment variables are there, let's see if the build pipeline works.

**Commit** and **Push** your changes back to GitHub. CircleCI should automatically spot it and kick into action.....Grab yourself a drink whilst it builds 🙌

### Step 3 - Review build and Container Registry

After you've had your drink, have a check of the pipeline - did it run sucessfully?

If it didn't have a look at the logs on CircleCI, some likely causes:

* Incorrect YAML indentation - That pesky YAML needs to be indented correct. Try comparing it with the example file.
* Missing or incorrect environment variable - Double check your CircleCI environment variables. Have you done them all? Are the name and values all correct?

If your build "went green" have a look at your container registry - you should see a brand new Docker image all ready to go 🚀 

In the images below you can see a succesful build in CircleCI, build number XXX which resulted in a Docker image with the tag 1.XXXX

![CircleCI Build Number](./images/circle_build_number_gcp.png "CircleCI Build Number")

![GCR Docker image tagged](./images/gcp_gcr_image_tag.png "GCR Docker image tagged")

### Step 4 - You did it!

You've done it 🚀 🚀 🚀 

You've successfully automated a continuous integration and continuous delivery pipeline. You could now use that Docker image and deploy it using your lovely GitOps pipeline!!!

Time to celebrate and don't forget those screenshots!!!



