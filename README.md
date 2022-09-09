RHEL-on-Azure---BYOS-vs-PAYGO

# Demo for deploying VMs to an Azure Resource group.
# This demo covers deployng images using Red Hat Cloud Access, Where you "Bring Your Own Subscription" (BYOS) to your certified cloud serice provider and...
# PAYGO images from Azure's Marketplace. These images are pre-configured to update from RHUI and are billed by the min. 
# While PAYGO will cost more money to run long term, it's a nice option to spin up short lived RHEL instances.

###############################################################################################################

# [Azure CLI install]
# first we need to install the azure CLI.
# Import the azure PGP public key on your RHEL system:
`sudo rpm --import https://packages.microsoft.com/keys/microsoft.asc`

# Enable the azure software repo for your version of RHEL
`cat /etc/redhat-release`
#       Red Hat Enterprise Linux release 8.6 (Ootpa)

# [RHEL 7]
`sudo dnf install -y https://packages.microsoft.com/config/rhel/7/packages-microsoft-prod.rpm`

# [RHEL8]
`sudo dnf install -y https://packages.microsoft.com/config/rhel/8/packages-microsoft-prod.rpm`

#[RHEL9]
`sudo dnf install -y https://packages.microsoft.com/config/rhel/9.0/packages-microsoft-prod.rpm`

# install azure-cli
`sudo dnf install -y azure-cli`

###############################################################################################################

# [Deploying w/ Azure CLI]
# first enable your red hat eligible subscriptions for cloud access use with your cloud provider. 
# This will unlock RHEL gold images to order from your CCSP (Certified cloud service provider) marketplace.
# to enable cloud access follow the instructions below 
# **Disclaimer** for up-to-date instructions please refernce: 
#       https://access.redhat.com/articles/5252771#:~:text=Log%20in%20to%20the%20Red,from%20the%20drop%2Ddown%20menu.

# Cloud Access Enrollment Process
Log in to the Red Hat Customer Portal. https://access.redhat.com/

Navigate to the Cloud Access Dashboard from the top menu bar. https://access.redhat.com/management/cloud

Click on 'Enable a new provider' to select the required Cloud Provider.

Select the Cloud Provider (AWS or Azure) from the drop-down menu.

Enter your Account Information for the Cloud Provider, including the account number. (You can enter up to 100 accounts at a time).

Choose the Subscription(s) you plan to deploy on the Cloud Provider. The list contains only the eligible subscriptions. To check the eligible products click here.

For each subscription you want to enable, enter the anticipated maximum number of Subscription Entitlements that you play to deploy on the Cloud Provider in the Maximum Enabled Entitlement Quantity field.

Click on Enable to request the process.

Next, click on the 3 dots besides Date Added and click on Activate Gold Image to complete the process.                  
