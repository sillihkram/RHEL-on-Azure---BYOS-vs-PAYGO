# RHEL-on-Azure-BYOS-vs-PAYGO
========

This is a Demo for deploying VMs to an Azure Resource group. This demo covers deployng images using Red Hat Cloud Access, Where you "Bring Your Own Subscription" (`BYOS`) to your certified cloud serice provider as well we demo deploying PAYGO images from Azure's Marketplace. These images are pre-configured to update from RHUI (Red Hat Update Infrastructure) and are billed by by microsoft by the min. While `PAYGO` will cost more money to run long term, it's a nice option to spin up short lived RHEL instances.


Azure CLI install
------------

Install the azure CLI.
Import the azure PGP public key on your RHEL system:

     sudo rpm --import https://packages.microsoft.com/keys/microsoft.asc

Enable the azure software repo for your version of RHEL

     cat /etc/redhat-release
     Red Hat Enterprise Linux release 8.6 (Ootpa)

[RHEL 7]

     sudo dnf install -y https://packages.microsoft.com/config/rhel/7/packages-microsoft-prod.rpm

[RHEL8]

     sudo dnf install -y https://packages.microsoft.com/config/rhel/8/packages-microsoft-prod.rpm

[RHEL9]

     sudo dnf install -y https://packages.microsoft.com/config/rhel/9.0/packages-microsoft-prod.rpm

Install azure-cli software package

     sudo dnf install -y azure-cli


Cloud Access Enrollment Process
------------

In order to use gold images in the marketplace you need to enable your red hat eligible subscriptions for cloud access. This process unlocks RHEL gold images that can be ordered from your CCSP (Certified cloud service provider) marketplace. to enable cloud access follow the instructions below 
**Disclaimer** for up-to-date instructions please refernce this link as the below instructions are subject to change: https://access.redhat.com/articles/5252771#:~:text=Log%20in%20to%20the%20Red,from%20the%20drop%2Ddown%20menu.

- Log in to the Red Hat Customer Portal: https://access.redhat.com/

- Navigate to the Cloud Access Dashboard from the top menu bar: https://access.redhat.com/management/cloud
     
- Click on 'Enable a new provider' to select the required Cloud Provider.

- Select the Cloud Provider (AWS or Azure) from the drop-down menu.

- Enter your Account Information for the Cloud Provider, including the account number. (You can enter up to 100 accounts at a time).

- Choose the Subscription(s) you plan to deploy on the Cloud Provider. The list contains only the eligible subscriptions. To check the eligible products click here.

- For each subscription you want to enable, enter the anticipated maximum number of Subscription Entitlements that you play to deploy on the Cloud Provider in the Maximum Enabled Entitlement Quantity field.

- Click on Enable to request the process.

- Next, click on the 3 dots besides Date Added and click on Activate Gold Image to complete the process.                  

Deploying Images on Azure using azure-cli
------------

Authenicate to your azure account using the az command (web browser required):

     az login
     
visit the URL provided, login with your azure account credentials and type the token provided from the command 
above.

List available resource groups for your acount 

     az group list

Create a resource group to deploy an instance of RHEL in:

     az group create -n TEST -l eastus
     
Now that we have a resource group let's take a look at the RHEL-BYOS images that are available:

     az vm image list --all --publisher RedHat --offer "rhel-byos"
     
Create a RHEL instance from a the latest RHEL8-LVM gold image. (optionally you can add --nowait to return your command quicker, however we want to see the IP information.

     az vm create -n RHEL-BYOS-TEST01 -g TEST --image redhat:rhel-byos:rhel-lvm8:latest --admin-username=azureuser --admin-password="Password123."
     
Now let's take a look at the VM we just created from the latest RHEL8 LVM gold image with -d (--show-details):

     az vm show -g TEST -n RHEL-BYOS-20220909 -d
     
Notice a few things
- licenseType: null
- publicIps:
- something-else??

Let's set the correct license type for this VM instance (RHEL_BYOS):

     az vm update -n RHEL-BYOS-20220909 -g TEST --license-type RHEL_BYOS
     
Login to the VM using the `publicIp` Address:

     ssh $(az vm list -d -g TEST -n RHEL-BYOS-20220909 --query publicIps) -l azureuser
     
setup the VM to consume a subscription and attach to insights

     sudo dnf repolist # 😕
     subscription-manager register
     dnf install rhc -y
     rhc connect # 🙂
     exit

Cleanup (delete) the VM we just created:
 
     az vm delete -g TEST -n RHEL-BYOS-20220909
