---
lab:
    title: 'Explore developer tools for workspace interaction
'
---

# Explore developer tools for workspace interaction

You can use various tools to interact with the Azure Machine Learning workspace. Depending on what task you need to perform and your preference for developer tool, you can choose which tool to use when. This lab is designed as an introduction to the developer tools commonly used for workspace interaction. If you want to learn how to use a specific tool in more depth, there are other labs to explore.

This lab will take approximately **30** minutes to complete.

## Before you start

You'll need an [Azure subscription](https://azure.microsoft.com/free) in which you have administrative-level access.

The commonly used developer tools for interacting with the Azure Machine Learning workspace are:
- Azure CLI with the Azure Machine Learning extension: This command-line approach is ideal for the automation of infrastructure.
- Azure Machine Learning Studio: Use the user-friendly UI to explore the workspace and all of its capabilities.
- Python SDK for Azure Machine Learning: Use to submit jobs and manage models from a Jupyter notebook, ideal for data scientists.

You'll explore each of these tools for tasks that are commonly done with that tool.

## Provision the infrastructure with the Azure CLI

For a data scientist to train a machine learning model with Azure Machine Learning, you'll need to set up the necessary infrastructure. You can use the Azure CLI with the Azure Machine Learning extension to create an Azure Machine Learning workspace and resources like a compute instance. 

To start, open the Azure Cloud Shell, install the Azure Machine Learning extension and clone the Git repo.

1. In a browser, open the Azure portal at [http://portal.azure.com](https://portal.azure.com/?azure-portal=true), signing in with your Microsoft account.
1. Select the [>_] (*Cloud Shell*) button at the top of the page to the right of the search box. This opens a Cloud Shell pane at the bottom of the portal.
1. The first time you open the cloud shell, you will be asked to choose the type of shell you want to use (*Bash* or *PowerShell*). Select **Bash**.
1. If you are asked to create storage for your cloud shell, check that the correct subscription is specified and select **Create storage**. Wait for the storage to be created.
1. To avoid any conflicts with previous versions, remove any ML CLI extensions (both version 1 and 2) with this command:
    ```
    az extension remove -n azure-cli-ml
    az extension remove -n ml
    ```
1. Install the Azure Machine Learning extension with the following command:
    ```
    az extension add -n ml -y
    ```
1. In the command shell, clone this Github repository to download all necessary files which are stored in the *Labs* folder.
    ```
    git clone https://github.com/MicrosoftLearning/dp100-explore-workspace.git dp100-explore-workspace
    ```
1. The files are downloaded to a folder named **dp100-explore-workspace**. To explore the files in your Cloud Shell storage and work with them, type the following command in the shell:
    ```
    code .
    ```
 



An Azure Machine Learning *workspace* provides a central place for managing all resources and assets you need to train and manage your models. You can provision a workspace using the interactive interface in the Azure portal, or you can use the Azure CLI with the Azure Machine Learning extension. In most production scenarios, it's best to automate provisioning with the CLI so that you can incorporate resource deployment into a repeatable development and operations (*DevOps*) process. 

In this exercise, you'll use the Azure portal to provision Azure Machine Learning to explore all options.

1. Sign into the [Azure portal](https://portal.azure.com).
2. Create a new **Machine Learning** resource with the following settings:
    - **Subscription**: *Your Azure subscription*
    - **Resource group**: *Create or select a resource group*
    - **Workspace name**: *Enter a unique name for your workspace*
    - **Region**: *Select the geographical region closest to you*
    - **Storage account**: *Note the default new storage account that will be created for your workspace*
    - **Key vault**: *Note the default new key vault that will be created for your workspace*
    - **Application insights**: *Note the default new application insights resource that will be created for your workspace*
    - **Container registry**: None (*one will be created automatically the first time you deploy a model to a container*)

> **Note**: When you create an Azure Machine Learning workspace, you can use some advanced options to restrict access through a *private endpoint* and specify custom keys for data encryption. We won't use these options in this exercise - but you should be aware of them!

3. Wait for the workspace and its associated resources to be created - this typically takes around 5 minutes. 

## Explore the Azure Machine Learning Studio

*Azure Machine Learning Studio* is a web-based portal through which you can access the Azure Machine Learning workspace. You can use the Azure Machine Learning Studio to manage all assets and resources within your workspace to train and deploy machine learning models. 

1. When the Azure Machine Learning workspace is created in the Azure portal, go to the resource group in which it has been created. Notice that the resource group contains your Azure Machine Learning workspace, an Application Insights, a Key Vault, and a Storage Account. 
1. Select your Azure Machine Learning workspace, and in its **Overview** page, select **Launch studio**. Another tab will open in your browser to open the Azure Machine Learning Studio.
1. On the left side of the Studio, the different pages within the Studio are shown. If only the symbols are visible in the menu, select the &#9776; icon to expand the menu and explore the names of the pages. 
1. Note the **Author** section, which includes **Notebooks**, **Automated ML**, and the **Designer**. These are the three ways you can create your own machine learning models within the Azure Machine Learning Studio.
1. Note the **Assets** section, which includes **Data**, **Jobs**, and **Models** among other things. Assets are either consumed or created when training or scoring a model. Assets are used to train, deploy, and manage your models and can be versioned to keep track of your history.
1. Note the **Manage** section, which includes **Compute** and **Datastores** among other things. These are infrastructural resources needed to train or deploy a machine learning model. 

## Author a training pipeline

To explore the use of the assets and resources in the Azure Machine Learning workspace, let's try and train a model. 

A quick way to author a model training pipeline is by using the **Designer**.

1. On the left side of the Studio, form the menu, select the **Designer** page.
1. You can create a new pipeline from scratch using prebuilt components, but you can also explore a sample. Select the **Regression - Automobile Price Prediction (Basic)** sample.
1. Note a new pipeline appears. At the top of the pipeline, a component is shown to load **Automobile price data (raw)**. Next, the pipeline processes the data and trains a linear regression model to predict the price for each automobile.
1. Select **Submit** at the top of the page. An error appears as you have not select a compute target yet. The pipeline can't run without compute resources. 

Let's create a compute target.

## Create a compute target

To run any workload within the Azure Machine Learning workspace, you'll need a compute resource. One of the benefits of Azure Machine Learning is the ability to create cloud-based compute on which you can run experiments and training scripts at scale.

1. In the Azure Machine Learning Studio, select the **Compute** page from the menu on the left side. There are four kinds of compute resources you can use:
    - **Compute instances**: A virtual machine managed by Azure Machine Learning. Ideal for development when you're exploring data and iteratively experimenting with machine learning models. 
    - **Compute clusters**: Scalable clusters of virtual machines for on-demand processing of experiment code. Ideal for running production code or automated jobs.
    - **Inference clusters**: A Kubernetes cluster used during inference. Ideal for real-time model deployment at a large scale.
    - **Attached compute**: Attach your existing Azure compute resources to the workspace, such as Virtual Machines or Azure Databricks clusters.

To train a machine learning model that you authored with the Designer, you can use either a compute instance or compute cluster.

2. On the **Compute instances** tab, add a new compute instance with the following settings. 
    - **Compute name**: *enter a unique name*
    - **Location**: *The same location as your workspace*
    - **Virtual machine type**: CPU
    - **Virtual machine size**: Standard_DS11_v2
    - **Total Available Quotas**: This shows dedicated cores available.
    - **Show advanced settings**: Note the following settings, but do not select them:
        - **Enable SSH access**: Unselected *(you can use this to enable direct access to the virtual machine using an SSH client)*
        - **Enable virtual network**: Unselected *(you would typically use this in an enterprise environment to enhance network security)*
        - **Assign to another user**: Unselected *(you can use this to assign a compute instance to a data scientist)*
        - **Provision with setup script**: Unselected *(you can use this to add a script to run on the remote instance when created)*

3. Wait for the compute instance to start and its state to change to **Running**.

> **Note**:
> Compute instances and clusters are based on standard Azure virtual machine images. For this exercise, the *Standard_DS11_v2* image is recommended to achieve the optimal balance of cost and performance. If your subscription has a quota that does not include this image, choose an alternative image; but bear in mind that a larger image may incur higher cost and a smaller image may not be sufficient to complete the tasks. Alternatively, ask your Azure administrator to extend your quota.

## Run your training pipeline

You've created a compute target and can now run your sample training pipeline in the Designer.

1. Navigate to the **Designer** page.
1. Select the **Regression - Automobile Price Prediction (basic)** pipeline draft.
1. Select **Settings** at the top right to expand the **Settings** pane.
1. Select **Compute instance** under **Select compute type**.
1. Select your newly created compute instance under **Select Azure ML compute cluster**. 
1. Select **Submit** to run the training pipeline again.
1. A pop-up will appear to set up the pipeline job. Configure and submit a new pipeline job with the following settings:
    - **Experiment**: Create new
    - **New experiment name**: `train-regression-designer`
    - Keep all other default settings.

The training pipeline will now be submitted to the compute instance. It will take approximately 10 minutes for the pipeline to complete. Let's explore some other pages in the meantime.

## Use jobs to view your history

Any time you run a script or pipeline in the Azure Machine Learning workspace, it's recorded as a **job**. Jobs allow you to keep track of the workloads you ran and compare them with each other. Jobs can belong to an **experiment**, which allows you to group job runs together.

1. Navigate to the **Jobs** page, using the menu on the left side of the Azure Machine Learning Studio.
1. Select the `train-regression-designer` experiment to view its job runs. Here, you'll see an overview of all jobs that are part of this experiment. If you ran multiple training pipelines, this view allows you to compare the pipelines and identify the best one.
1. Select the last job in the `train-regression-designer` experiment.
1. Note that the training pipeline is shown where you can view which components ran successfully or failed. If the job is still running, you can also identify what is currently being run.
1. To view the pipeline job details, select the **Job overview** at the top right to expand the **Pipeline job overview**. 
1. Note that in the **Overview** parameters, you can find the job's status, who created the pipeline, when it was created and how long it took to run the complete pipeline (among other things).

When you run a script or pipeline as a job, you can define any inputs and document any outputs. Azure Machine Learning also automatically keeps track of your job's properties. By using jobs, you can easily view your history to understand what you or your colleagues have already done. During experimentation, jobs help keep track of the different models you train to compare and identify the best model. During production, jobs allow you to check whether automated workloads ran as expected.

1. When your job is completed, you can also view the details of each individual component run, including the output. Feel free to explore the pipeline to understand how the model is trained.

## Stop your compute instance

If you've finished exploring Azure Machine Learning for now, you should shut down your compute instance to avoid incurring unnecessary charges in your Azure subscription.

1. In Azure Machine Learning studio, on the **Compute** page, select your compute instance.
2. Click **Stop** to stop your compute instance. When it has shut down, its status will change to **Stopped**.

> **Note**: Stopping your compute ensures your subscription won't be charged for compute resources. You will however be charged a small amount for data storage as long as the Azure Machine Learning workspace exists in your subscription. If you have finished exploring Azure Machine Learning, you can delete the Azure Machine Learning workspace and associated resources. However, if you plan to complete any other labs in this series, you will need to repeat this lab to create the workspace and prepare the environment first.