# Using Oracle Functions

In this lab you will configure, build, and deploy a serverless function to the OCI infrastructure. Oracle Functions, Fn for the open-source, is currently in a Limited Availabily state.  At present, Oracle Functions is only available in the Phoenix region.

There are three main sections in this lab; Set up your tenancy, Set up your client, and Create, deploy, and invoke your function. For you to have a successful lab it is important to make sure you follow the steps closely. As previously stated, this is a Limited Availability release and therefore is not streamlined in the setup and configuration.

In the ```OracleFunctions``` directory is a pdf document, ```Oracle_Functions_Overview.pdf``. This document is step-by-step in setting up, configuring, and building and deploying your serverless function.  

Since we are already in the Phoenix region we are already subscribed to the Limited Availability and have been whitelisted.

## Create IAM Policies

Open the pdf document, ```Oracle_Functions_Overview.pdf``` and navigate to page two ```Create IAM policies```. All previous steps have already been completed by nature of being in the Phoenix region and VCNs have already been created.

For the ```intvravipati/root``` compartment I have already created the two policies required for the root compartment.  These policies are 

1. ```functions-developers-ocir-access```: This policy gives users access to the OCIR in our tenancy
2. ```functions-service-policy```: This policy allows Oracle Functions to OCIR in our tenancy

If you are using the ```aura-test``` compartment the two policies you DO NOT need to create the specific compartment policies mentioned in the next step of ```Create IAM policies```. I have already created these policies.  These policies are shown below:

1. ```functions-developers-access```
2. ```functions-developers-FaaS-access```

Again, if you are using the aura-test compartment you do not need to create these policies, but I would recommend that you have a look at these policies.

Now, if you are ***NOT*** using the ```aura-test``` compartment you must create the two policies specific to your compartment.

Once you have created the policies, ***if required for your compartment***, then follow the remainder of the instructions in the pdf document.