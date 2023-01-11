# AWS IoT Greengrass Installation on Terabox Gateway

The following guide provides the detailed installation procedure of AWS IoT Greengrass V2 Core software on Terabox Gateway. 

## [**Pre-requisites**](#pre-requisites)

To install AWS Greengrass on device, below pre-requisites has to be installed on the device

- An AWS account. If you don't have one, please follow [**Setup AWS Account**](https://docs.aws.amazon.com/greengrass/v2/developerguide/getting-started.html#getting-started-set-up-aws-account) for creating an aws account
- AWS region which supports Greengrass V2. Please see [**AWS IoT Greengrass V2 endpoints and quotas**](https://docs.aws.amazon.com/general/latest/gr/greengrassv2.html) in the AWS General Reference for the list of supported regions.
- An AWS Identity and Access Management (IAM) User with Administrator privileges. Please see [**creating an IAM role**](#creating-an-iam-role) 
- Terabox Gateway for installing Greengrass software
- Python3.6 or greater version
- AWS CLI. Follow the instructions [**aws cli**](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html) if not installed

This guide contains 2 methods to provison the device under Greengrass V2 resources Group

User can choose any of the below two methods as per their convenience, if user is familiar with AWS, they can choose <Manual Provision> procedure else can follow the <Automatic provision> procedure

### [**Automatic Provison**](#installing-through-terminal-commands)
### [**Manual Provison**](#installing-through-aws-iam-console)

### Creating an IAM Role

1. Sign in to the [**IAM console**](https://console.aws.amazon.com/iam/) as the account owner by choosing Root user and entering your AWS account email address. On the next page, enter your password.

2. In the navigation pane, choose **Users** and then choose **Add users**.

3. For User name, enter the desired name. For example, **GreengrassProv**

4. Select the check box next to **Access Key Progrmatic Access**. 

5. Choose **Next: Permissions**.

6. Under Set permissions, choose **Attach Existing Policies Directly**. Filter the desired policies from the list. Select following policies from the menu
    - IoTFullAccess
    - S3 FullAccess
    - GreengrassFullAccess
    - IAMFullAccess

7. Choose **Next: Tags** and then **Next: Preview**.
8. choose **Create user**.
9. Download the **AWS Security credentials** from the next window. These credentials are required for setting up Greengrass core on the Terabox.

## Installing Greengrass Core Software on Terabox

AWS IoT Greengrass Core can be installed on the Terabox using 2 ways

### **Installing Through AWS IAM Console**

1. Preparing the Terabox
    - Create the default system user and group that runs components on your device.
    ```bash
    sudo useradd --system --create-home ggc_user
    sudo groupadd --system ggc_group
    sudo usermod -aG docker idt_ggc_user
    sudo visudo
    root ALL=(ALL:ALL) ALL
    ```
2. Provide AWS Credentials to Terabox
    - Copy the **ACCESS_KEY** & **SECURITY_ID** from **csv file** downloaded from the **Pre-requisites** step and export as environment variables
    ```bash
    export AWS_ACCESS_KEY_ID=<ACCESS-KEY>
    export AWS_SECRET_ACCESS_KEY=<SECURITY-ACCESS-KEY>
    ```
3. Setting up Environment for Greengrass
    - Java runtime has to be installed on the Terabox to install Greengrass core SDK. 
    - SSH to Terabox and run the following command
    ```bash
    sudo apt-get install default-jdk
    ```
    - This will install Latest version of Java runtime on to Terabox

4. Installing Greengrass core in Terabox
    - Sign in to the [**IAM console**](https://console.aws.amazon.com/iam/) as the account owner by choosing Root user and entering your AWS account email address. On the next page, enter your password.
    - Choose **IoT Greengrass** from the search menu
    - Choose **Setup One Core Device** in the next page. 
    - Provide **Core device name** in Step-1 or keep the automatically generated name by AWS
    - Provide **Thing Group Name** by creating a new group or assign to an existing group
    - Select the Operating system. Terabox runs on **Linux**. So, select **Linux**.
    - Copy the **curl** command mentioned under **Download the Installer** section and run on Terabox
    ```bash
    cd /opt/aws

    curl -s https://d2s8p88vqu9w66.cloudfront.net/releases/greengrass-nucleus-latest.zip > greengrass-nucleus-latest.zip && unzip greengrass-nucleus-latest.zip -d GreengrassInstaller
    ```
    - Copy the **installer** command in **Run the Installer** section and execute on Terabox
    ```bash
    cd /opt/aws

    sudo -E java -Droot="/opt/aws/greengrass/v2" \
    -Dlog.store=FILE -jar ./GreengrassInstaller/lib/Greengrass.jar\
    --aws-region us-east-1 \
    --thing-name GreengrassQuickStartCore-183270afee6 \
    --thing-group-name GreengrassQuickStartGroup \
    --component-default-user ggc_user:ggc_group \
    --provision true \
    --setup-system-service true \
    --deploy-dev-tools true
    ```
    - This will take sometime to install AWS IoT Greengrass Core software on Terabox
5. Verifying the Installation
    - Once the greengrass core software is installed successfully, it will create a systemd service.
    - To check the status of the service
    ```bash
    sudo service greengrass status
    ```
    - To view the log messages of the AWS IoT Greengrass Core software
    ```bash
    sudo journalctl -u greengrass.service -f
    ```

### **Installing Through Terminal Commands**
1. Follow Steps 1,2,3 mentioned in [**Installing Through AWS IAM Console**](#installing-through-aws-iam-console) for installing the dependencies and setting up the environment
2. Exceute the following command which will download the Greengrass into Terabox
```bash
cd /opt/aws

curl -s https://d2s8p88vqu9w66.cloudfront.net/releases/greengrass-nucleus-latest.zip > greengrass-nucleus-latest.zip && unzip greengrass-nucleus-latest.zip -d GreengrassInstaller
```
3. Execute the following command which will install the Greengrass into Terabox
```bash
cd /opt/aws

sudo -E java -Droot="/opt/aws/greengrass/v2" \
-Dlog.store=FILE -jar ./GreengrassInstaller/lib/Greengrass.jar \
--aws-region us-east-1 \
--thing-name GreengrassQuickStartCore-183270afee6 \
--thing-group-name GreengrassQuickStartGroup \
--component-default-user ggc_user:ggc_group \
--provision true \
--setup-system-service true \
--deploy-dev-tools true
```
4. Repeat Step 5 from [**Installing Through AWS IAM Console**](#installing-through-aws-iam-console) for verifying the installation & checking the logs.
