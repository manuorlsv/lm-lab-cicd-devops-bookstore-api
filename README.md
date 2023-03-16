# CI/CD with CircleCI

## Introduction

In this guided set of instructions we're going to:

* Introduce a few Python unit tests 
* Configure a CI/CD tool (in our case [CircleCI](https://circleci.com/)) to build the application and run the unit tests
* Configure the CircleCI tool to also build the docker image and push to your container registry

## Instructions

For following through these instructions you can take two approaches:

1. Fork this repository into your own GitHub account and then read through the files and steps. Utilising this repository means everything is already set-up.

2. Follow through the instructions and apply the changes to your already existing bookstore API repository.

We'd recomend taking option 2 above as it will help piece together the job of the different files but if you have any problems then option 1 (the end result) is there too.

### Step 1 - Create the unit tests

Firstly lets introduce a few Python unit tests. 

Don't worry if you don't code in Python we'll guide you through it and the focus for the DevOps programme isn't really Python, we're just using it as a means by which to have an application to be built and tested.

Head over to the [unit test instructions](./docs/UNITTESTS.md) to add in the tests

### Step 2 - CircleCI setup

Now you've got a few unit tests we can start the work on configuring a CI/CD tool. In the case of this tutorial, we'll utilise [CircleCI](https://circleci.com/).

Head over to the [CircleCI instructions](./docs/CICD.md) to get things setup.

### Step 3 - CircleCI pushing docker image

The next phase is Cloud provider specific. Here you'll update your pipeline to push to your target container registry.

For AWS, head over to the [AWS and Circle instructions](./docs/AWSCIRCLEPUSH.md)

For Google Cloud, head over to the [GCP and Circle instructions](./docs/GCPCIRCLEPUSH.md)

For Azure, head over to the [Azure and Circle instructions](./docs/AZURECIRCLEPUSH.md)