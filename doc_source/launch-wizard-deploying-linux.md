# Access and deploy an application with AWS Launch Wizard for SQL Server on Linux<a name="launch-wizard-deploying-linux"></a>

**Topics**
+ [Access AWS Launch Wizard](#accessing-launch-wizard-linux)
+ [Deploy AWS Launch Wizard on Linux](#deploy-console-launch-wizard-linux)
+ [Post\-deployment cluster tasks](#launch-wizard-linux-post-deployment)

## Access AWS Launch Wizard<a name="accessing-launch-wizard-linux"></a>

You can launch AWS Launch Wizard from the following locations\.
+ **AWS Console\.** From the [AWS Management Console](https://console.aws.amazon.com) under **Management and Governance**\.
+ **AMI launch page in EC2 console\.** From the Launch Wizard banner that appears when you select **AMIs**, under **Images**, in the EC2 console\. The banner appears when you search for SQL AMIs\. 
+ **AWS Launch Wizard landing page\.** From the AWS Launch Wizard page, located at [https://aws\.amazon\.com/launchwizard/](https://aws.amazon.com/launchwizard/)\.

## Deploy AWS Launch Wizard on Linux<a name="deploy-console-launch-wizard-linux"></a>

The following steps guide you through a SQL Server Always On application deployment with AWS Launch Wizard on the Linux platform after you have launched it from the console\. For SQL Server deployments on Linux, you must use an instance type built on the [Nitro System](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-types.html#ec2-nitro-instances)\. EBS volumes are exposed as NVMe block devices on instances built with the Nitro System\. Device names that are specified for NVMe EBS volumes in a block device mapping are renamed using NVMe device names \(`/dev/nvme[[0-26]n1`\)\. Launch Wizard deployments on Linux do not support block devices on Xen\-virtualized instances\. 

1. When you select **Create deployment** from the AWS Launch Wizard landing page, you are directed to the **Choose application** page where you are prompted to select the type of application that you want to deploy\. Select **Microsoft SQL Server Always On**\.

1. Select the operating system on which you want to install the SQL Server Always On application — in this case, **Linux**\.

1. After you select the operating system, under **Permissions**, Launch Wizard displays the AWS Identity and Access Management \(IAM\) policy required for Launch Wizard to access other AWS services on your behalf\. For more information about setting up IAM for Launch Wizard, see [AWS Identity and Access Management \(IAM\)](launch-wizard-setting-up.md#launch-wizard-iam)\. Select **Next** \.

1. After selecting the type of application to deploy and the operating system, you are prompted to enter specifications for the new deployment on the **Configure application settings** page\. The following tabs provide information about the specification fields\.

------
#### [ General ]
   + **Deployment name**\. Enter a unique application name for your deployment\.
   + **Simple Notification Service \(SNS\) topic ARN \(Optional\)**\. Specify an SNS topic where AWS Launch Wizard can send notifications and alerts\. For more information, see the [https://docs.aws.amazon.com/sns/latest/dg/welcome.html](https://docs.aws.amazon.com/sns/latest/dg/welcome.html)\.

------
#### [ Connectivity ]

   Enter specifications for how you want to connect to your application instance and what kind of Virtual Private Cloud \(VPC\) you want to set up\. 

   **Key pair name**
   + Select an existing key pair from the dropdown list or create a new one\. If you select **Create new key pair name** to create a new key pair, you are directed to the Amazon EC2 console\. From there, under **Network and Security**, choose **Key Pairs**\. Choose **Create a new key pair**, enter a name for the key pair, and then choose **Download Key Pair**\.
**Important**  
This is the only chance for you to save the private key file, so be sure to download it and save it in a safe place\. You must provide the name of your key pair when you launch an instance, and provide the corresponding private key each time that you connect to the instance\. 

     Return to the Launch Wizard console and choose the refresh button next to the **Key Pairs** dropdown list\. The newly created key pair appears in the dropdown list\. For more information about key pairs, see [Amazon EC2 Key Pairs and Windows Instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html)\.

   **Virtual Private Cloud \(VPC\)**\. Choose whether you want to use an existing VPC or create a new VPC\.
   + **Select Virtual Private Cloud \(VPC\)** option\. Choose the VPC that you want to use from the dropdown list\. Your VPC must contain one public subnet and, at least, three private subnets\. The private subnets must have outbound connectivity to the internet and other AWS services \(S3, CFN, SSM, Logs\)\. We recommend that you enable this connectivity with a NAT Gateway\. For more information about NAT Gateways, see [NAT Gateways](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html) in the Amazon VPC User Guide\.
     + **Public Subnet**\. Your VPC must contain one public subnet and, at least, three private subnets\. Choose a public subnet for your VPC from the dropdown list\. To continue, you must select the check box that indicates that the Public subnet has been set up and each of the selected private subnets have outbound connectivity enabled\. 

**To add a new public subnet**

       If the traffic of a subnet is routed to an internet gateway, the subnet is known as a public subnet\. If, however, a subnet doesn't have a route to the internet gateway, the subnet is known as a private subnet\. To use an existing VPC that does not have a public subnet, you can add a new public subnet using the following steps\.
       + Follow the steps in [Creating a Subnet in the Amazon VPC User Guide](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Internet_Gateway.html#Add_IGW_Create_Subnet) using the existing VPC you intend to use AWS Launch Wizard\.
       + To add an internet gateway to your VPC, follow the steps in [Attaching an Internet Gateway](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Internet_Gateway.html#Add_IGW_Attach_Gateway) in the Amazon VPC User Guide\.
       + To configure your subnets to route internet traffic through the internet gateway, follow the steps in [Creating a Custom Route Table](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Internet_Gateway.html#Add_IGW_Routing) in the Amazon VPC User Guide\. Use IPv4 format \(0\.0\.0\.0/0\) for Destination\.
       + The public subnet should have the “auto\-assign public IPv4 address” setting enabled\. To enable this setting, follow the steps in [Modifying the Public IPv4 Addressing Attribute for Your Subnet](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-ip-addressing.html#subnet-public-ip) in the Amazon VPC User Guide\.
     + **Availability Zone \(AZ\) configuration**\. You must choose at least three Availability Zones, with one private subnet for each zone that you select\. From the dropdown lists, select the **Availability Zones** within which you want to deploy your **primary** and **secondary**, and **configuration** nodes\.

**To create a private subnet**

       If a subnet doesn't have a route to an internet gateway, the subnet is known as a private subnet\. To create a private subnet, you can use the following steps\. We recommend that you enable the outbound connectivity for each of your selected private subnets using a NAT Gateway\. To enable outbound connectivity from private subnets with public subnet, see the steps in [Creating a NAT Gateway](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html#nat-gateway-creating) to create a NAT Gateway in your chosen public subnet\. Then, follow the steps in [Updating Your Route Table ](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html#nat-gateway-create-route)for each of your chosen private subnets\.
       + Follow the steps in [Creating a Subnet](https://docs.aws.amazon.com/vpc/latest/userguide/working-with-vpcs.html#AddaSubnet) in the Amazon VPC User Guide using the existing VPC you will use in AWS Launch Wizard\. 
       + When you create a VPC, it includes a main route table by default\. On the **Route Tables** page in the Amazon VPC console, you can view the main route table for a VPC by looking for **Yes** in the **Main** column\. The main route table controls the routing for all subnets that are not explicitly associated with any other route table\. If the main route table for your VPC has an outbound route to an internet gateway, then any subnet created using the previous step, by default, becomes a public subnet\. To ensure the subnets are private, you may need to create one separate route table for all of your private subnets\. This route table must not contain any routes to an internet gateway\. Verify that all of the private subnets have the same route table association\.
   + **Create new Virtual Private Cloud \(VPC\)** option\. Launch Wizard creates your VPC\. You can optionally enter a VPC name tag\.

------
#### [ SQL Server ]

**SQL Server configuration**
   + **User name and password**\. By default, Launch Wizard applies the user name `sa` \. This system administrator account is used for SQL Server management\. Create a complex password that is at least 8 characters long, and then reenter the password to verify it\. See [Password Policy](https://docs.microsoft.com/en-us/sql/relational-databases/security/password-policy?view=sql-server-2017) for more information\.
   + **Floating IP Address**\. This field is available when you select a Virtual Private Cloud \(VPC\)\. The IP address that you enter is used as the endpoint for your SQL Server Availability Group listener\. Launch Wizard creates a route from this IP address to the SQL primary node in your route table\. Verify that the IP address is not already used within your VPC and is outside of all of the provided subnet CIDRs\.
   + **Amazon Machine Image \(AMI\)**\. Select the version of Microsoft SQL Server Enterprise to deploy\. You can select an AMI from either the license\-included or custom AMI lists\. 

**Pacemaker cluster configuration**

   Pacemaker is a high\-availability cluster resource manager\. This software runs on a set of hosts, or cluster of nodes, to preserve integrity and minimize the downtime of selected services, or resources\. Pacemaker is maintained by the [ClusterLabs](https://www.clusterlabs.org/) community\.
   + **Pacemaker cluster name**\. Enter a name to identify your pacemaker cluster\.
   + **Pacemaker cluster user name**\. By default, Launch Wizard applies the pacemaker user name `hacluster`\. This user name is used to securely communicate between cluster nodes\. 
   + **Pacemaker cluster password**\. Create a complex password that is at least 8 characters long, and then reenter the password to verify it\. See [Password Policy](https://docs.microsoft.com/en-us/sql/relational-databases/security/password-policy?view=sql-server-2017) for more information\. 

**SQL \- Pacemaker cluster connection settings**

   After you configure Pacemaker cluster and SQL Server, you must create a user in SQL Server to communicate with Pacemaker\.
   + **SQL Pacemaker user name and password**\. Enter a user name for SQL Server to communication with the Pacemaker cluster\. Create a complex password that is at least 8 characters long, and then reenter the password to verify it\. See [Password Policy](https://docs.microsoft.com/en-us/sql/relational-databases/security/password-policy?view=sql-server-2017) for more information\.
   + **S3 location for node certificates**\. An Amazon S3 bucket location is required by the SQL nodes to share self\-signed certificates with each other\. Provide the bucket or object locations and verify that the names begin with `launchwizard`\.

**Additional SQL Server settings \(optional\)**
   + **Nodes**\. Enter a **Primary SQL node name**, a **Secondary SQL node name**, and a **Configuration node name**\. 
   + **Additional naming**\. Enter a **Database name** and an **Availability group name**\.

------

1. When you are satisfied with your configuration selections, select **Next**\. If you don't want to complete the configuration, select **Cancel**\. When you select **Cancel**, all of the selections on the specification page are lost and you are returned to the landing page\. To go to the previous screen, select **Previous**\.

1. After configuring your application, you are prompted to define the infrastructure requriements for the new deployment on the **Define infrastructure requirements** page\. The following tabs provide information about the specification fields\.

------
#### [ Storage and Compute ]

   You can choose to select your instance and volume types, or to use AWS recommended resources\. If you choose to use AWS recommended resources, you have the option of defining your high availability cluster needs\. If no selections are made, default values are assigned\.
   + **Number of instance cores**\. Choose the number of CPU cores for your infrastructure\. The default value assigned is 4\. 
   + **Network performance**\. Choose your preferred network performance in Gbps\.
   + **Expected RAM size \(Memory\)**\. Choose the amount of RAM that you want to attach to your EC2 instances\. The default value assigned is 4 GB\.
   + **Type of storage drive**\. Select the storage drive type for the SQL data, logs, and tempdb volumes\. The default value assigned is SSD\. 
   + **SQL Server throughput**\. Select the sustained SQL Server throughput that you need\. 
   + **Recommended resources**\. Launch Wizard displays the system\-recommended resources based on your infrastructure selections\. If you want to change the recommended resources, select different infrastructure requirements\. 
   + 

**Volume sizes**
     + **Volume size**\. Select the size of the SQL Server data volume in Gb for **Temporary database**, **Logs**, **Data**, and **Backup** volumes\.
     + **Provisioned IOPS\. ** Select the IOPS value of the SQL Server data volume in IOPS for **Temporary database**, **Logs**, **Data**, and **Backup** volumes\.

       For throughput limits and volume characteristics, see [Amazon EBS Volume Types](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-volume-types.html) in the Amazon EC2 User Guide\.

------
#### [ Tags\-Optional ]

   You can provide optional custom tags for the resources Launch Wizard creates on your behalf\. For example, you can set different tags for EC2 instances, EBS volumes, VPC, and subnets\. If you select **All**, you can assign a common set of tags to your resources\. Launch Wizard assigns tags with a fixed key LaunchWizardResourceGroupID and value that corresponds to the ID of the AWS Resource Group created for a deployment\. Launch Wizard does not support custom tagging for root volumes\. 

------
#### [ Estimated On\-Demand Cost to Deploy Additional Resources ]

   AWS Launch Wizard provides an estimate for application charges incurred to deploy the selected resources\. The estimate updates each time you change a resource type in the Wizard\. The provided estimates are only for general comparisons\. They are based upon On\-Demand costs and your actual costs may be lower\. 

------

1. When you are satisfied with your infrastructure selections, select **Next**\. If you don't want to complete the configuration, select **Cancel**\. When you select **Cancel**, all of the selections on the specification page are lost and you are returned to the landing page\. To go to the previous screen, select **Previous**\.

1. On the **Review and deploy** page, review your configuration details\. If you want to make changes, select **Previous**\. To stop, select **Cancel**\. When you select **Cancel**, all of the selections on the specification page are lost and you are returned to the landing page\. If you are ready to deploy, read and select the check box next to the Acknowledgment\. Then choose **Deploy**\.

1. Launch Wizard validates the inputs and notifies you if something must be addressed\. 

1. When validation is complete, Launch Wizard deploys your AWS resources and configures your SQL Server Always On application\. Launch Wizard provides you with status updates about the progress of the deployment on the Deployments page\. From the Deployments page, you can also view the list of current and previous deployments\.

1. When your deployment is ready, you see notification that your SQL Server Always On application was successfully deployed\. If you have set up SNS notification, you are also alerted through SNS\. You can manage and access all of the resources related to your SQL Server Always On application by selecting **Manage**\.

1. When the SQL Server Always On application is deployed, you can access your Amazon EC2 instances through the EC2 console\. You can also use [AWS Systems Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/what-is-systems-manager.html) to manage your SQL Server Always On application for future updates and patches through built\-in integration via resource groups\.

## Post\-deployment cluster tasks<a name="launch-wizard-linux-post-deployment"></a>

The Launch Wizard Pacemaker implementation includes three cluster nodes: primary, secondary, and configuration only\. The primary node provides the Microsoft SQL Server for Linux resource and the floating IP address\. To ensure that the cluster operates correctly, some administrative tasks must be performed in a specific way\. If these tasks are performed incorrectly, then Pacemaker may identify the activity as a resource failure and attempt to fail over the resources to the secondary node\. If the resources are failed over to the secondary node, the cluster can remain in an unknown state, which can impact user access\. 

There are four primary tasks: Start Cluster, Stop Cluster, Move Resources, and Recovery\. These tasks must be carried out by a sudo user with an SSH connection to any of the cluster nodes\. Before performing any of these tasks, verify the cluster status using `pcs resource status --all`\. Running this command returns all cluster issues\. All issues must be addressed prior to performing any administrative tasks\.

**Start cluster**

1. Log in to a cluster node using a sudo user over an SSH connection\.

1. Verify that all cluster nodes are available\.

1. Verify cluster status using the following command: `pcs resource --all`\.

   Address all issues before attempting to start the cluster\.

1. Start all cluster nodes using the following command: `pcs cluster start --all --wait`\.

1. Verify that the cluster has started using the following command: `pcs resource --all`\. 

   The output provides information about the cluster nodes and cluster resources\. All cluster nodes should be online and all resource agents should be visible and allocated to their assigned cluster nodes\.

1. Verify that the availability group listener is available by pinging the floating IP address\.

**Manually move cluster resources**

1. Log in to a cluster node using a sudo server over an SSH connection\.

1. Verify that all cluster nodes are available\.

1. Verify cluster status using the following command: `pcs resource --all`\.

   Address all issues before attempting to start the cluster\.

1. Run the following command: `pcs resource move <RESOURCE_NAME> <NODE_NAME> --force`\.

   This command moves the resource agent to **<NODE\_NAME>** and starts the resource\. All cluster constraints will be applied\. If the Microsoft SQL Server resource agent is moved, then the Availability Group listener will follow\.

1. Verify cluster status using the following command: `pcs resource --all`\.

   The resource that was moved should be located on the **<NODE\_NAME>**\.

1. Clear temporary constraints using the following command: `pcs resource clear <RESOURCE_NAME>`\.

**Stop cluster**

1. Log in to a cluster node using a sudo server over an SSH connection\.

1. Verify that all cluster nodes are available\.

1. Verify cluster status using the following command: `pcs resource --all`\.

   Address all issues before attempting to start the cluster\.

1. Stop the cluster using the following command: `pcs cluster stop --ALL`\. This will gracefully shut down all of the cluster nodes\.

1. Verify the shut down status using the following command: `pcs status --all`\.

   This command should return that the cluster is no longer running\. 

**Recovery**

If a node is restarted from the operating system or the AWS Management Console, the Pacemaker node and its related services will not automatically start\. This prevention protects the high availability database replicas from split\-brain corruption\.

The following steps are required to restore the cluster to normal operations\. 

1. Log in to a cluster node using a sudo server over an SSH connection\.

1. Determine the node that was restarted using the following command: `pcs resource --ALL`\. The restarted node will be offline\.

1. Verify cluster status using the following command: `pcs resource --all`\.

   Address all issues before attempting to start the cluster\.

1. Start the restarted node using the following command: `pcs cluster start --<NODE_NAME>`\.

1. Verify cluster status using the following command: `pcs resource --all`\.

   Address all issues before attempting to start the cluster\.

1.  If the restarted node is the primary node of the cluster, then the Availability Group resource must be returned to the primary node\. 

1.  Remove all temporary constraints using the following commands: `pcs resource clear <AG_RESOURCE>` and `pcs resource clear <AG_LISTENER>`\.

1. Run the following command: `pcs resource move <RESOURCE_NAME> <PRI_NODE_NAME> --force`\. 

   This command moves the resources to `<PRI_NO_NAME>` and starts the resource\. Any cluster constraints are applied\. In this scenario, if the Microsoft SQL Server resource agent is moved, then the Availability Group listener follows\.

1. Verify cluster status using the following command: `pcs resource --all`\. The restarted node will be located on `<PRI_NO_NAME>`\.
