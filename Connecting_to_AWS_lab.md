# Connecting to AWS

## Introduction

This lab guides you through the steps involved in connecting to an AWS EC2 F1 instance, and starting and stopping the instance.

## Objectives

After completing this lab, you will be able to:

- Connect to an AWS EC2 F1 instance using the provided credentials
- Start an instance
- Start a RDP (Remote Desktop Protocol) session
- Close the RDP session
- Stop the instance

## Step 1: Login into the AWS and starting an F1 instance

#### Each registered participant has been allocated a preconfigured EC2 F1 instance and should have received an email with the following details:
- Account ID, 
- IAM username, 
- Link to access a preconfigured EC2 F1 instance

### 1.1. Open a web browser and login into AWS EC2 F1 instance using the provided credentials
**1.1.1.** Start a web browser session

**1.1.2.** Either click on the provided link to open up an AWS login page OR enter [https://console.aws.amazon.com/ec2](https://console.aws.amazon.com/ec2) to open a login page

If you had used the link then you should see a login page similar to shown here:

![alt tag](./images/connecting_lab/FigConnectingLab-1.png)
#### Figure 1. Login page accessed through the provided link

If you had not used the link you may be directed to the AWS standard login page

![alt tag](./images/connecting_lab/FigConnectingLab-2.png)
#### Figure 2. Entering credentials manually

Enter *xilinx-aws-f1-developer-labs* in the Email address field and click **Next** to see the login page similar to shown in **Figure 1**

**1.1.3.** Enter _userxx_ in the **IAM user name** field and enter the provided password in the **Password** field

**1.1.4.** Click **Sign In**
### 1.2. Make sure to select N. Virginia (or instructor indicated region) as the region and start the instance
**1.2.1.** In the top right corner, using the drop-down button, select a region with F1 instances, such as **N. Virginia** (**US East)**
![alt tag](./images/connecting_lab/FigConnectingLab-3.png)
#### Figure 3. Selecting region

If you select different region other then where the accounts are created for then you may not see your instance as well as the source files which are pre-loaded for the workshop.

**1.2.2.** Click on the **EC2** link on the dashboard or if not visible, then click on the _Services_ drop-down button and then click on **EC2**

![alt tag](./images/connecting_lab/FigConnectingLab-4-1.png)   ![alt tag](./images/connecting_lab/FigConnectingLab-4-2.png)
#### Figure 4. Accessing EC2 service

**1.2.3.** Click on the **Instances** link on the left panel

![alt tag](./images/connecting_lab/FigConnectingLab-5.png)
#### Figure 5. Accessing Instances

You may see several instances

**1.2.4.** Enter your username in the filter field just below the **Launch Instance** button and hit enter
![alt tag](./images/connecting_lab/FigConnectingLab-6.png)
#### Figure 6. Filtering your instance

**1.2.5.** Making sure that your instance is selected, click on the **Actions &gt; Instance State &gt; Start**
![alt tag](./images/connecting_lab/FigConnectingLab-7.png)
#### Figure 7. Starting an instance

**1.2.6.** Click on the **Yes, Start** button

**1.2.7.** Click on the refresh button(![alt tag](./images/Fig-refresh.png)) to see the updated status to _Running_

![alt tag](./images/connecting_lab/FigConnectingLab-8.png)
#### Figure 8. Running state

**1.2.8.** Make a note of the Public DNS and IPv4 Public IP which will be used by PuTTy and Remote Desktop (RDP)
![alt tag](./images/connecting_lab/FigConnectingLab-9.png)
#### Figure 9. Assigned IP to the running instance

## Step 2: Interacting with the Instance using RDP

**You can communicate with the instance using command line through PuTTY or Git Bash, and using GUI through remote desktop (RDP) connection.**

**2.1. Start a remote desktop session**

**2.1.1.** Start the remote desktop session

**2.1.2.** Enter the _IPv4_ address

**2.1.3.** Click on the **Show Options**

![alt tag](./images/connecting_lab/FigConnectingLab-10.png)
#### Figure 10. Entering the IPv4 address

**2.1.4.** Select the **Display** tab and select _True Color (24 bit)_ and click **Connect**
![alt tag](./images/connecting_lab/FigConnectingLab-11.png)
#### Figure 11. Selecting resolution and connecting

**2.1.5.** A certificate warning will be displayed. Click **Yes** to open the RDP session

**2.1.6.** Enter centos as the username and enter the provided password and click **OK**

![alt tag](./images/connecting_lab/FigConnectingLab-12.png)
#### Figure 12. Entering username and password

**2.1.7.** Right-click on the desktop and select **Open Terminal** to open a window

**2.1.8.** You should enter the following commands in any newly opened terminal window to source the environments

   ```
      cd ~/aws-fpga	  
      source sdaccel_setup.sh	  
      source $XILINX_SDX/settings64.sh
   ```

## Step 3: Stopping the Instance and Signing Out

**It is important to shut down the instance in order to stop billing meter**

### You do not need to execute this command in this lab as you will continue the session for the supsequent labs

### 3.1. Shut down the RDP instance from the terminal window.

**3.1.1.** Type the following command to terminate the RDP session and shutdown the instance

   ```
      sudo shutdown now	  
   ```
   
**3.1.5.** Check the browser window, you will see status as either **Stopping** or **Stopped.** Click on the refresh button to see the status update  
![alt tag](./images/connecting_lab/FigConnectingLab-13-1.png)   ![alt tag](./images/connecting_lab/FigConnectingLab-13-2.png)
#### Figure 13. Instance Status

**3.1.6.** Once the instance is stopped, sign out by clicking on the drop-down button on the top bar and selecting **Sign Out**
![alt tag](./images/connecting_lab/FigConnectingLab-14.png)
#### Figure 14. Signing out

## Conclusion

In this lab, you learned how to connect to an AWS EC2 F1 instance, interact with the instance using RDP connection, how to stop the instance, and sign out.

---------------------------------------

<p align="center"><b>
Start the next lab: <a href="Makefile_Flow_lab.md">2. Makefile Flow</a>
</b></p>

---------------------------------------

## Appendix: Interacting with the Instance using Putty

**A.1 Connect using PuTTY.**

**A.1.1.** Start PuTTY program

**A.1.2.** Enter _centos@&lt;public\_dns\_entry&gt;_ in the **Host Name** field and **22** in the _Port_ field

Make sure that SSH is selected as the Connection type
![alt tag](./images/connecting_lab/FigConnectingLab-15.png)
#### Figure 15. Session settings in PuTTY

**A.1.3.** Expand **SSH** under the _Connection_ in the left panel and click **Auth**

**A.1.4.** Click on the **Browse…** button, browse to where the private key has been stored

If you don&#39;t have the private key file (as in workshop) you can skip this step

**A.1.5.** Click **Open**

![alt tag](./images/connecting_lab/FigConnectingLab-16.png)
#### Figure 16. Selecting private key file

**A.1.6.** Click **Yes**

The PuTTY window will open. It will ask for the password (in case of the workshop). Enter the provided password
![alt tag](./images/connecting_lab/FigConnectingLab-17.png)
#### Figure 17. The PuTTY window showing the connection

**A.1.7.** Set password for the RDP connection by entering sudo passwd &lt;your choice of password&gt; command. You will use the same password in the RDP connection.

**A.1.8.** Enter **exit** to close the session
