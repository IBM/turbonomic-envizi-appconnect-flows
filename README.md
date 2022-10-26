# Turbonomic - Envizi Integration

## Table of contents

<!--ts-->
  * [Overview](#overview)
  * [Turbonomic Configuration](#turbonomic-configuration)
  * [AppConnect Configuration](#appconnect-configuration)
  * [Post-Setup Instructions](#post-setup-instructions)

<!--te-->

## Overview
- Flows included in this integration:
    - [TurbonomicLocations](./TurbonomicLocations.md)
    - [TurbonomicAccounts](./TurbonomicAccounts.md)

_Click on the flow names above to see the flow related details_

## Turbonomic Configuration

- Turbonomic v8.6.5 or higher is required for this integration to work
- Create a user in Turbonomic with "Observer" role. The AppConnect will be configured to use this user's credentials to fetch the necessary data.
- For accurate emission calculations from Envizi, add the following Tag Categories in vCenter and add their values as tags to the Data Centers:
    - `Country`: Name of Country
    - `Latitude`: Latitude in Decimal Degrees format
    - `Longitude`: Longitude in Decimal Degrees format

    ![vCenter Tags](https://media.github.ibm.com/user/375131/files/0eb7bd00-5141-11ed-9b2c-4bde7c361f33)

**Note:** Tags sync from vCenter to Turbonomic might take upto 20 minutes.


## AppConnect Configuration

For help, refer to the [AppConnect official documentation](#appconnect-official-documentation) to perform the below operations:

1. Add Account for "Amazon S3" connector
    - Credentials will be given by Envizi
2. Add an Account for "HTTP" connector
    - This account will be used for Authentication
    - **API Key**: Enter the Turbonomic credentials in this format `<USERNAME>&password=<PASSWORD>`
        - e.g., If the username is `johndoe` and the password is `TheCakeIsALie`, enter `johndoe&password=TheCakeIsALie`
    - **API Key Location**: Select "body URL encoded"
    - **API Key Name**: Enter the text `username` and **not** your actual username.
    ![Account Configuration example](https://media.github.ibm.com/user/375131/files/c26d7c80-5142-11ed-87b2-d32c7d2798b8)
    
3. Add another Account for "HTTP" connector
    - This account will be used for other API calls
    - Leave all authentication related fields empty
4. Import the flows
    - Navigate to the flows below and use the link to their raw files for importing in AppConnect:
        - [TurbonomicLocations](./AppConnect%20Flows/TurbonomicLocations.yaml)
        - [TurbonomicAccounts](./AppConnect%20Flows/TurbonomicAccounts.yaml)
5. Configure the flow to use the right accounts
    - For all Amazon S3 nodes in the flow, select the account created in Step 1 
    - For the first HTTP node in the flow, select the account created in Step 2
    - For all other HTTP nodes in the flow, select the account created in Step 3
6. Configure the scheduler
    - Click on the "Scheduler" node

    <img width="659" alt="Scheduler node" src="https://media.github.ibm.com/user/375131/files/ea694d80-01ed-11ed-8852-16b625339675">

    - Configure the schedule as needed

    <img width="659" alt="Scheduler configuration 2" src="https://media.github.ibm.com/user/375131/files/00c3d900-01ef-11ed-86aa-29008ff86450">

    - The TurbonomicAccounts flow must be executed daily, preferably at UTC midnight between 00:05 and 00:30
    - The TurbonomicLocations flow can be configured to run once a month or even On-demand as described [here](#on-demand-data-pull)
    - **Note:** By default, the flow will run when it is started. To change this behavior, untick the below checkbox in the "Scheduler" node. It is recommended to keep this checked for On-demand data pull
    ![Run on Start checkbox](https://media.github.ibm.com/user/375131/files/f010f080-5157-11ed-8325-66c9c74f6243)

7. Configure the flow parameters
    - Click on the "Set variable" node
    ![Set variable configuration](https://media.github.ibm.com/user/375131/files/09576580-50a2-11ed-8f46-b3beb969f206)

    - In Variable > config > customer, enter the value provided by Envizi
    - In Variable > config > url, enter URL for the Turbonomic instance
        - e.g., https://example.com:9080

    - Scroll to the Amazon S3 node and click on it to open the configuration
    ![S3 Configuration](https://media.github.ibm.com/user/375131/files/56c06800-4bb4-11ed-9aa6-c6758d61300a)

    - From the bucket dropdown, select the bucket name provided by Envizi
    - Perform these actions on all Amazon S3 nodes in the flow

8. Start the flow

Once the flow it started, it will run as configured in scheduler. However, the flow will only collect data from the day it has been started.

In order to pull any historical data, additional configurations described in [On-demand Data Pull](#on-demand-data-pull) need to be done.



### AppConnect official documentation

- [Adding Accounts](https://www.ibm.com/docs/en/app-connect/containers_cd?topic=scratch-connecting-accounts)
- [Importing Flow](https://www.ibm.com/docs/en/app-connect/containers_cd?topic=designer-exporting-importing-flows#importingflow)

### On-demand Data Pull

Use the on-demand data pull when there is historical energy related data that needs to be loaded from Turbonomic to Envizi.

- This method is for the initial sync or to sync data that was added/updated between specific dates.
- This method is meant to be executed just once whenever needed and then stopped.
- AppConnect logs will show when On-demand load has been completed successfully or that it can be stopped.

- For TurbonomicLocations, no additional configuration is needed.

- To use this method in flows other than TurbonomicLocations, the following parameters in "Set variable" node need to be configured:
  - Variable > config > OverrideStartDate:
    - The start timestamp of the window between which the data will be pulled from.
    - The date must be specified in yyyy-mm-dd format.
    - e.g., `2022-06-26`
  - Variable > config > OverrideEndDate:
    - The end timestamp of the window between which the data will be pulled from.
    - The date must be specified in yyyy-mm-dd format.
    - e.g., `2022-06-27`
- **Note:** The dates configured here will be considered as UTC

    ![Set variable configuration](https://media.github.ibm.com/user/375131/files/09576580-50a2-11ed-8f46-b3beb969f206)


## Post-Setup Instructions

The Envizi implementation team will support the next steps of the integration by provisioning the file transfer details, enabling Envizi data connectors and validating the Envizi configuration ready to receive the data flow.

Please reach out to the primary contact at Envizi who can help coordinate next steps.