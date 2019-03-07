# Creating AFI Image after Building Full System

This document guides you through the steps involved in creating an AFI which can be run AWS EC2 F1 instance to verify the design works in hardware. It assumes that a full system is built which consists of an host application and xclbin. 

### Create an Amazon FPGA Image (AFI)

To execute the application on F1, the following files are needed:

- Host application (exe)
- FPGA binary (xclbin)
- Amazon FPGA Image (awsxclbin)

The xclbin and the host applications must already have been generated 

1. Create a **xclbin** directory under the <project> directory using the following commands:
   ```
      cd /home/centos/aws-fpga/<project_dir>
      mkdir xclbin
   ```
1. Copy the generated **xclbin** file and the host application into the created **xclbin** directory, using the following commands
   ```
      cd xclbin
      cp /home/centos/aws-fpga/<project_system_built_dir_path>/*.xclbin .
      cp /home/centos/aws-fpga/<project_system_built_dir_ath>/*.exe .
   ```
### Create an AFI by running the create\_sdaccel\_afi.sh script and wait for the completion of the AFI creation process
1. Enter the following command to generate the AFI:
   ```
      $SDACCEL_DIR/tools/create_sdaccel_afi.sh –xclbin=binary_container_1.xclbin –s3_bucket=<bucket-name> -s3_dcp_key=<dcp-folder-name> -s3_logs_key=<logs-folder-name>
   ```
In the above command, replace binary\_container\_1.xclbin with an appropriate name if it is different; &lt;bucket-name&gt;, &lt;dcp-folder-name&gt;, and &lt;logs-folder-name&gt; with the names you had given when running CLI script.  Learn more about setting up S3 buckets at [https://github.com/aws/aws-fpga/blob/master/SDAccel/docs/Setup_AWS_CLI_and_S3_Bucket.md](https://github.com/aws/aws-fpga/blob/master/SDAccel/docs/Setup_AWS_CLI_and_S3_Bucket.md)  
In the workshop environment this was already done.  

The create\_sdaccel\_afi.sh script does the following:

- Starts a background process to create the AFI
- Generates a \_afi\_id.txt which contains the FPGA Image Identifier (or AFI ID) and Global FPGA Image Identifier (or AGFI ID) of the generated AFIs
- Creates the \*.awsxclbin AWS FPGA binary file which will need to be read by the host application to determine which AFI should be loaded in the FPGA.
- Enter the following command to note the values of the AFI IDs by opening the *\_afi\_id.txt file.  
   ```
      cat *afi_id.txt
   ```
1. Enter the **describe-fpga-images** API command to check the status of the AFI generation process:
   ```
      aws ec2 describe-fpga-images --fpga-image-ids <AFI ID>
   ```
Note: When AFI creation completes successfully, the output should contain:
   ```
      ...
      "State": {
          "Code": "available"
      },
      
      ...
   ```  

Wait until the AFI becomes available before proceeding to execute on the F1 instance.

