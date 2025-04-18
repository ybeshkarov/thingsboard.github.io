---
layout: docwithnav-pe
title: Installing ThingsBoard PE from AWS Marketplace
description: Installing ThingsBoard PE from AWS Marketplace
redirect_from: "/docs/user-guide/install/aws-marketplace-pe/"
---

This guide describes how to install ThingsBoard Professional Edition from AWS Marketplace. 
Using this guide you will install [BYOL](https://docs.aws.amazon.com/marketplace/latest/userguide/pricing.html#ami-pricing-models) version of the product.
Basically, you get the license directly from ThingsBoard, Inc, but purchase corresponding server instances and infrastructure from AWS.       

* TOC
{:toc}

## Prerequisites

- Active [Amazon AWS](https://aws.amazon.com/){:target="_blank"} account

## Step 1. Subscribe to ThingsBoard PE BYOL

Open [ThingsBoard Professional Edition BYOL](https://aws.amazon.com/marketplace/pp/B07V8S6JLG) product page on [AWS Marketplace](https://aws.amazon.com/marketplace).

- Click the **Continue to Subscribe** button

## Step 2. General configuration

- Review and Accept all Terms and Conditions. Click the "Accept Terms" button.

- Leave Fulfillment Option and Software Version as-is.

- Select the region from the list of available AWS regions. 

- Click the **Continue to Launch** button

## Step 3. Launch your ThingsBoard PE instance 

### Step 3.1. Choose your Region

Make sure you review the usage instructions. It is always a good idea to copy them to a safe place. 

### Step 3.2. Choose your EC2 Instance Type

You can optionally change your EC2 Instance Type, VPC and Subnet. This step is usually for advanced AWS EC2 users.  

{% capture vm-min-req %}
ThingsBoard requires EC2 instance with at least **4GB of RAM**; consider [adjusting the memory parameters](/docs/user-guide/install/pe/ubuntu/#step-6-optional-memory-update-for-slow-machines-4gb-of-ram) to ensure stability.
For optimal performance, we recommend an instance with at least **8GB of RAM** and **2 vCPUs**.
{% endcapture %}
{% include templates/info-banner.md content=vm-min-req %}

### Step 3.3. Configure Security Group Settings

Make sure you create new Security Group based on seller settings. 

- Click the **Create New Based on Seller Settings** button

![image](/images/user-guide/install/aws-marketplace-pe/tb-pe-mk-launch-security.png)

Populate necessary Security Group name and details and save new group

![image](/images/user-guide/install/aws-marketplace-pe/tb-pe-mk-new-security-group.png)

Click the "Save" button.

### Step 3.4. Configure Key Pair Settings

You can optionally select different or create new Key Pair for your instance. 
Make sure you have access to the key file before you proceed. 
We will use the key file later in this guide. 

### Step 3.5. Launch your ThingsBoard PE Instance

Finally, click the "Launch" button.

![image](/images/user-guide/install/aws-marketplace-pe/tb-pe-mk-launch-launch.png)

### Step 3.6. Open EC2 console

Once Launch is complete, you can navigate to EC2 console to find out the Public IP address of the newly created instance.

![image](/images/user-guide/install/aws-marketplace-pe/tb-pe-mk-launch-complete.png)

### Step 3.7. Obtain your Public IP and EC2 Instance ID

In AWS EC2 console you need to wait until the instance state changes to **running** and all Status checks will be finished.

![image](/images/user-guide/install/aws-marketplace-pe/tb-pe-mk-ec2-console.png)

On the image above example instance has this Public DNS name 

- **ec2-18-197-23-51.eu-central-1.compute-1.amazonaws.com**

Instance ID 

- **i-032b8bbf297987458**

Please save your Instance ID and Public DNS name to a safe place. We will use them later in this guide.  

## Step 4. Obtain your license secret

In order to activate your ThingsBoard instance you will need to get the license secret. 
ThingsBoard Licenses are managed by [ThingsBoard License Portal](https://license.thingsboard.io/signup).   

Please register on [ThingsBoard License Portal](https://license.thingsboard.io/signup) to get your license. 
See [How-to get pay-as-you-go subscription](https://www.youtube.com/watch?v=dK-QDFGxWek){:target="_blank"} or [How-to get perpetual license](https://www.youtube.com/watch?v=GPe0lHolWek){:target="_blank"} for more details.
 
Please save your license secret to a safe place. We will use it later in this guide.
 
## Step 5. Configure your license secret

Once you get the license secret, you should put it to the thingsboard configuration file. 

### Step 5.1. Connect to your ThingsBoard Instance over SSH

Please use the official guides: 

  * [Connecting to Your Linux Instance from Windows Using PuTTY](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/putty.html) - for Windows users;
  * [Connecting to Your Linux Instance Using SSH](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstancesLinux.html) - For Linux/Mac users.
  
Note: You will need to use instance Public DNS name (see [Step 3.7](/docs/user-guide/install/pe/aws-marketplace/#step-37-obtain-your-public-ip-and-ec2-instance-id)) and the key file (see [Step 3.4](/docs/user-guide/install/pe/aws-marketplace/#step-34-configure-key-pair-settings))

### Step 5.2. Put your license secret to ThingsBoard configuration file

Open the file for editing using the following command:

```bash 
sudo nano /etc/thingsboard/conf/thingsboard.conf
``` 
{: .copy-code}

Locate the following configuration block:

```bash
# License secret obtained from ThingsBoard License Portal (https://license.thingsboard.io)
# UNCOMMENT NEXT LINE AND PUT YOUR LICENSE SECRET:
# export TB_LICENSE_SECRET=
```

and put your license secret. Please don't forget to uncomment the export statement. See example below: 

```bash
# License secret obtained from ThingsBoard License Portal (https://license.thingsboard.io)
# UNCOMMENT NEXT LINE AND PUT YOUR LICENSE SECRET:
export TB_LICENSE_SECRET=YOUR_LICENSE_SECRET_HERE
``` 

## Step 6. Launch ThingsBoard service  

Execute the following command to start ThingsBoard:

```bash
sudo service thingsboard start
```
{: .copy-code}

{% capture 90-sec-ui %}
Please allow up to 120 seconds for the Web UI to start. This is applicable only for slow machines with 1-2 CPUs or 1-2 GB RAM.{% endcapture %}
{% include templates/info-banner.md content=90-sec-ui %}

## Step 7. Connect to ThingsBoard UI

Now you can open this link in your browser:

- http://PUBLIC_DNS_NAME/login

In this example:

- http://ec2-18-197-23-51.eu-central-1.compute-1.amazonaws.com/login

Use this login to connect as system Administrator 

- **sysadmin@thingsboard.org**

Default password for System Administrator is Instance ID (see [Step 3.7](/docs/user-guide/install/pe/aws-marketplace/#step-37-obtain-your-public-ip-and-ec2-instance-id)). In this example: 

-  **i-032b8bbf297987458**

Now you can proceed to the next steps.

## Post-installation steps

{% capture elastic-ip-note %}
By default, AWS Marketplace instances **do not assign a static public IP**. This means that the **public IP may change upon instance restart**.  
To ensure a persistent public IP, you must **assign an Elastic IP** to your EC2 instance. You can follow the steps in the [AWS documentation](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/working-with-eips.html).  
{% endcapture %}

{% include templates/info-banner.md content=elastic-ip-note %}

**Configure HAProxy to enable HTTPS**

 * Step 1. Assign valid domain name system (DNS) record to your instance external IP address.
 * Step 2. Connect to your instance using SSH or PuTTY (see [Step 5.1](/docs/user-guide/install/pe/aws-marketplace/#step-51-connect-to-your-thingsboard-instance-over-ssh)).
 * Step 3. Execute the following commands:
 ```bash
 sudo certbot-certonly --domain smth.yourcompany.com --email support@yourcompany.com
 sudo haproxy-refresh
 ```
    where **smth.yourcompany.com** is your DNS name from the second step
    and **support@yourcompany.com** is your email to get notifications from [certbot](https://certbot.eff.org/).   
 
## Frequently Asked Questions


**How can I enable free trial?**

Customers may still use <a href="https://thingsboard.cloud" target="blank">ThingsBoard Cloud</a> for that. 
30 days of seamless experience and the newest features, except white-labeling, from the latest source code!
<br><br>

**What is the Total Cost of Ownership (TCO) for my TB PE instance?**

<p>The typical total cost of ownership consists of:</p>
<ul>
    <li>TB License fee - see <a href="/pricing">pricing</a></li>
    <li>AWS EC2 instance price - we believe <a href="https://www.ec2instances.info/">ec2instances.info</a> is a convenient resource to compare prices.</li>
    <li>Additional EC2 costs for network traffic, disk space and other optional services (Cloud Watch or similar)</li>
</ul>
<p>Example A: The price for ThingsBoard PE instance with Maker subscription plan based on t3.medium with 20 GB disk will cost approximately $42.4 per month:</p>
<ul>
    <li>$10.00 for monthly ThingsBoard PE Maker subscription fee</li>
    <li>$30.368 for 1 month of t3.medium usage</li>
    <li>$2.0 for 20 GB of EBS volume per month</li>
</ul>
<p>Example B: The price for ThingsBoard PE instance with Prototype subscription plan based on m5.large with 100 GB disk will cost approximately $179 per month:</p>
<ul>
    <li>$99.00  for monthly ThingsBoard PE Prototype subscription fee</li>
    <li>$70.08 for 1 month of m5.large usage</li>
    <li>$10.0 for 100 GB of EBS volume per month</li>
</ul>
<p>All prices are in USD.</p>

<br>


**How to connect to my new ThingsBoard PE instance using SSH?**

See <a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstancesLinux.html">official documentation page</a> for more details.
<br><br>

**How to connect to my new ThingsBoard PE instance using PuTTY?**

See <a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/putty.html">official documentation page</a> for more details.
<br><br>

**How to enable HTTPS?**

See <a href="/docs/user-guide/install/pe/aws-marketplace/#post-installation-steps">Post-installation steps</a>
<br><br>

**How do I configure my TB PE instance?**

See <a href="/docs/user-guide/install/config/">official documentation page</a> for more details.

<br>

**How do I get software updates for my TB PE instance?**

You will receive an email notification about new software versions once it becomes available. 
This email notification will contain a link to <a href="/docs/user-guide/install/pe/upgrade-instructions/">official documentation page</a> with the upgrade instructions.
<br><br>

**How do I backup my database?**

You can follow <a href="/docs/user-guide/install/pe/upgrade-instructions/#prepare-for-upgrading-thingsboard-centos-ubuntu">backup instructions</a> available with upgrade instructions.
For additional guidance, you can also refer to the official <a href="https://www.postgresql.org/docs/16/backup.html">PostgreSQL backup documentation</a>.

<br><br>

**How do I upgrade my instance type?**

To upgrade your EC2 instance type, follow these steps:

1. Stop the ThingsBoard Service

    Before changing the instance type, stop the ThingsBoard service to ensure a clean shutdown:

    ```bash
    sudo service thingsboard stop
    ```
    {: .copy-code}

2. Stop the EC2 Instance

    Go to the AWS EC2 Console, select your instance, and stop it:

    - Navigate to EC2 Dashboard
    - Select the instance you want to upgrade
    - Click Instance state → Stop instance

3. Change the Instance Type

    Once the instance is stopped, update its type:

    - In the EC2 Console, go to Actions → Instance settings → Change instance type
    - Select the desired instance type
    - Click Change

4. Start the Instance

<br><br>

**Where is my ThingsBoard instance logs?**

ThingsBoard logs are stored in the <i>/var/log/thingsboard</i> folder. 
For more details on managing logs and configuring log levels, refer to the <a href="/docs/pe/user-guide/troubleshooting/#logs">Troubleshooting Guide</a>
<br><br>

**How do I get professional support?**

Please review ThingsBoard professional <a href="/docs/services/support/">support plans</a> and <a href="/docs/contact-us/">contact us</a>.
<br><br>

## Next steps

{% assign currentGuide = "InstallationGuides" %}{% include templates/guides-banner.md %}
