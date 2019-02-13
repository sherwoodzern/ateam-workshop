# Using Oracle Functions

In this lab you will configure, build, and deploy a serverless function to the OCI infrastructure. Oracle Functions, Fn for the open-source, is currently in a Limited Availabily state.  At present, Oracle Functions is only available in the Phoenix region.

There are three main sections in this lab; Set up your tenancy, Set up your client, and create, deploy, and invoke your function. For you to have a successful lab it is important to make sure you follow the steps closely. As previously stated, this is a Limited Availability release; therefore, there are many steps in getting everything setup properly, both in the OCI platform and on your local box.

In the ```OracleFunctions``` directory there is a pdf document, ```Oracle_Functions_Overview.pdf```. This document is a step-by-step guide to setting up, configuring, and building and deploying your serverless function.  

Since we are already in the Phoenix region we are already subscribed to the Limited Availability release and have been whitelisted.

## Create IAM Policies

Open the pdf document, ```Oracle_Functions_Overview.pdf``` and navigate to page two ```Create IAM policies```. All previous steps have already been completed by nature of being in the Phoenix region and VCNs have already been created.  I have also assigned everybody to the ```Functions_Developers``` group.

For the ```intvravipati/root``` compartment I have already created the two policies required for the root compartment.  These policies are 

1. ```functions-developers-ocir-access```: This policy gives users access to the OCIR in our tenancy
2. ```functions-service-policy```: This policy allows Oracle Functions to OCIR in our tenancy

If you are using the ```aura-test``` compartment you DO NOT need to create the two compartment specific policies. I have already created these policies.  These policies are shown below:

1. ```functions-developers-access```
2. ```functions-developers-FaaS-access```

Again, if you are using the aura-test compartment you do not need to create these policies, but I would recommend that you have a look at these policies.

Now, if you are ***NOT*** using the ```aura-test``` compartment you must create the two compartment specific policies.

Once you have created the policies, ***if required for your compartment***, then follow the remainder of the instructions in the pdf document.