# **ADS AWS - CLI & Kubernetes Setup Runbook**

For managing Kubernetes resources in MCE (PCCP) clusters
### **Prerequisites**
#### **System Requirements**
- macOS/Linux with Homebrew (Apple internal version).
- Shell: bash or zsh.
### **Tools Installation**

Run the following commands to install dependencies:

#### 1. Install Homebrew (Apple internal)
**Follow Instruccions for Apple internal Homebrew:**
```
[Apple Internal Homebrew Setup Guide](https://github.pie.apple.com/homebrew/brew)
```
#### 2. Install AWS CLI and plugins
```
brew install awscli.
```
#### **AWS & Apple Connect Setup**

1. Configure awsappleconnect
```
git clone git@github.pie.apple.com:CloudTech/aws-apple.git
cd aws-apple/setup
bash setup.sh
```

2. Input below details for testing:
```
Apple Connect username: [your_username]
Mascot role name: custom_role
Dev AWS Account ID : Skip / Press Enter
Test AWS Account ID: 964795336004
Prod AWS Account ID : Skip / Press Enter
Profile Name: acs-dp-cr-auth-test-6004
```
3. Verify there is n error in your installation
```
Example for above:
Keerthis-M1:setup keerthi$ bash setup.sh


AWS@Apple Setup

1.In order to configure your AWS CLI access, we'll need to gather some information 
from you. If you have any questions, go to: 

https://github.pie.apple.com/CloudTech/aws-apple/tree/main/setup


2. Enter your AppleConnect username [default 'k_poludasu']:  

3. In particular, to find your role and account IDs, go to: 

https://portal.aws.ais.apple.com/console-access

4. Enter the MASCOT role name (ex. developer_role): custom_role

5. Now you'll need to enter the AWS Account IDs for your accounts. Typically customers have Dev, Test, and Prod accounts. If you don't, just press enter.

Enter your Dev AWS Account ID (ex. 123456789012): 
Enter your Test AWS Account ID (ex. 234567890123): 964795336004
Enter your Prod AWS Account ID (ex. 345678901234): 
Enter your Sandbox AWS Account ID (ex. 456789012345): acs-dp-cr-auth-test-6004


Would you like to append the AWS environment (i.e. [dev]) to your BASH 
prompt? We think most people should set this to true, however if you 
use a customized PS1 setting, set this to false.
Use the recommended PS1 setting (choose true or false): true

If you would like to name your profiles, you can set an optional prefix. If not, just
press enter, and we'll label your accounts 'dev', 'test', 'prod', and 'sandbox'.
(Optional) Enter your preferred profile name prefix (ex. mascot-):     

AWS@Apple Homebrew configuration 
```
# Install Kubectl
`brew install kubectl`

#### Install Kubectx and Kubens tools
```
GIT => <https://github.com/ahmetb/kubectx>
brew install kubectx
```
####Install fzf - fuzzy search tool
```
brew install fzf
```

#### (GKE Only) Google Cloud SDK
```
<https://cloud.google.com/sdk/docs/install>
```
###(KCS Only) Apple Cloud CLI (ACC)
```
brew update && brew install apple/isd-dev/acc
```
####(Kube Only) Install kcli
```
brew tap hybrid-cloud/tools git@github.pie.apple.com:hybrid-cloud/homebrew-tap.git
brew install apple/aci/kcli
```

### **Setup aws profile**

Add following command aliases to your shell

`(~/.zshrc or ~/.bashrc)`

alias aws-profile='export AWS_PROFILE=$(grep -o "[profile.*]" ~/.aws/config | tr -d "[]profile " | fzf —no-mouse)‘
alias aws-eks='aws eks update-kubeconfig —name $(aws eks list-clusters —output=text | awk '''{print $2}''' | fzf —no-mouse)‘

# reload profile
```
source ~/.zshrc # or ~/.bashrc
```
### **Command Usage**
```
# To list and select available AWS profiles
aws-profile

# To list and select from available EKS cluster
aws-eks

# To list and select Kubernetes(K8s) namespaces
kubens
# Once namespace is set you w'ont need to run -n <namespace> with kubectl every time.

# To switch kubectl context to other previous configure cluster use
kubectx
## **Druid config for SRE Local Profile Setup**
```
AWS profile
```
################## ADP DEV ##################
[profile adp-dev-admin]
credential_process = awsappleconnect -u <username> -a 471112717795 -r admin
region = us-west-2

################## ADP RVW ##################
[profile adp-rvw-admin]
credential_process = awsappleconnect -u <username> -a 381492187087 -r admin
region = us-west-2

################## ADP STG ##################
[profile adp-stg-admin]
credential_process = awsappleconnect -u <username> -a 772127642300 -r admin
region = us-west-2

################## ADP PRD ##################
[profile adp-prd-admin]
credential_process = awsappleconnect -u <username> -a 590184080396 -r admin
region = us-west-2

#################

################## ADP DEV ##################
[profile adp-dev-admin]
credential_process = awsappleconnect -u <appleConnect> -a 471112717795 -r admin
region = us-west-2

[profile Ads-Bot-Infra-Prov-adp-dev]
credential_process = awsappleconnect -u <appleConnect> -a 471112717795 -r admin
region = us-west-2

################## ADP RVW ##################
[profile adp-rvw-admin]
credential_process = awsappleconnect -u <appleConnect> -a 381492187087 -r admin
region = us-west-2

[profile Ads-Bot-Infra-Prov-adp-rvw]
credential_process = awsappleconnect -u <appleConnect> -a 381492187087 -r admin
region = us-west-2

################## ADP STG ##################
[profile adp-stg-admin]
credential_process = awsappleconnect -u <appleConnect> -a 772127642300 -r admin
region = us-west-2

[profile Ads-Bot-Infra-Prov-adp-stg]
credential_process = awsappleconnect -u <appleConnect> -a 772127642300 -r admin
region = us-west-2

################## ADP PRD ##################
[profile adp-prd-admin]
credential_process = awsappleconnect -u <appleConnect> -a 590184080396 -r admin
region = us-west-2

[profile Ads-Bot-Infra-Prov-adp-prd]
credential_process = awsappleconnect -u <appleConnect> -a 590184080396 -r admin
region = us-west-2
```

EKS alias to all clusters in ADS
```
alias kc-adpd-admin="aws eks --region us-west-2 update-kubeconfig --name pccp-stride-demandplat-dev-cluster --profile adp-dev-admin"
alias kc-adpr7-admin="aws eks --region us-west-2 update-kubeconfig --name pccp-stride-demandplat-qa-cluster --profile adp-rvw-admin"
alias kc-adpr9-admin="aws eks --region us-west-2 update-kubeconfig --name pccp-stride-demandplat-r9-cluster --profile adp-rvw-admin"
alias kc-adps4-admin="aws eks --region us-west-2 update-kubeconfig --name pccp-stride-demandpla-stg-cluster --profile adp-stg-admin"
alias kc-adps6-admin="aws eks --region us-west-2 update-kubeconfig --name pccp-stride-demandplat-s6-cluster --profile adp-stg-admin"
alias kc-adps7-admin="aws eks --region us-west-2 update-kubeconfig --name pccp-stride-demandplat-s7-cluster --profile adp-stg-admin"
alias kc-adps8-admin="aws eks --region us-west-2 update-kubeconfig --name pccp-stride-demandplat-s8-cluster --profile adp-stg-admin"
alias kc-adpp1-admin="aws eks --region us-west-2 update-kubeconfig --name pccp-stride-demandplat-p1-cluster --profile adp-prd-admin"
alias kc-adpp2-admin="aws eks --region us-west-2 update-kubeconfig --name pccp-stride-demandplat-p2-cluster --profile adp-prd-admin"
alias kc-adpr10-admin="aws eks --region us-west-2 update-kubeconfig --name pccp-stride-demandplat-r10-cluster --profile adp-rvw-admin"
```
