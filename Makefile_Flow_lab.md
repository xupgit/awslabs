# Makefile Flow

## Introduction

This lab guides you through the steps involved in using a Makefile flow to build and perform CPU and hardware emulations to verify the functionality. You will then use an AWS F1 instance to validate the design.

## Objectives

After completing this lab, you will be able to:

- Run CPU Emulation to verify the functionality of a design using a Makefile flow
- Run HW Emulation to verify the functionality including kernel hardware using a Makefile flow
- Build the full system and verify functionality in hardware on an AWS F1 instance

## Procedure

This lab is separated into steps that consist of general overview statements that provide information on the detailed instructions that follow. Follow these detailed instructions to progress through the lab.

This lab comprises three primary steps: You will source the environment settings, then you will build and run emulation flows using Makefile, you will next test the application on the F1 instance. The Appendix section lists steps involved in building a full system using Makefile flow including creating an Amazon FPGA Image (AFI).

## Step 1: Source Environment Settings        

### 1.1. Open a Terminal window. Source the SDAccel settings and create a directory called Makefile\_flow under _~/aws-fpga_. Change the directory to the newly created directory. Copy the helloworld\_ocl directory from /home/centos/sources to ~/aws-fpga/Makefile\_flow directory.

**1.1.1.** Right-click on the Centos desktop and select **Open Terminal**

**1.1.2.** Execute the following commands in the terminal window to source the Xilinx tools

   ```
      cd ~/aws-fpga	  
      source sdaccel_setup.sh		  
      source $XILINX_SDX/settings64.sh
   ```

**1.1.3.** Create a **Makefile\_flow** directory and change the working directory into it using the following commands:

   ```
      mkdir Makefile_flow	  
      cd Makefile_flow		  
   ```

**1.1.4.** Copy the provided helloworld\_ocl project directory into the current working directory using the following command:

   ```
      cp -r /home/centos/sources/helloworld_ocl/ .
   ```
## Step 2: Build and Run Emulation Flows using Makefile Flow

The SDAccel emulation flows allow testing, profiling and debugging of the application before deploying on F1.

Software emulation allow functionality of the software application to be verified.

Hardware emulation allow the verification of the functionality of the generated logic generated for the FPGA and the application working together.

### 2.1. Using the command line, run a makefile flow to perform CPU Emulation.

**2.1.1.** Execute the following commands in the terminal to build and run the *SW (CPU) emulation* flow for the SDAccel &#39;hello world&#39; example:

   ```
      cd helloworld_ocl
      make clean
      make check TARGETS=sw_emu DEVICES=$AWS_PLATFORM all
   ```

The application will be compiled, the xclbin and the helloworld.exe files will be generated. The application will be executed on CPU in software emulation mode showing output like:

![alt tag](./images/makefile_lab/FigMakefileLab-1.png)
#### Figure 1. Executing the application in software emulation mode

**2.1.2.** Go to the _src_ folder and open the **host.cpp** file to see its content. Observe that the _DATA\_SIZE_ is defined as **256** (line 34), one operand (source\_a) is defined as constant **10** (line 46) and another operand (source\_b) as constant **32** (line 47), providing a result of 42

![alt tag](./images/makefile_lab/FigMakefileLab-2.png)
#### Figure 2. Program snippet

**2.1.3.** Change the _DATA\_SIZE_ to **64** , _source\_a_ to **4** and _source\_b_ to **15,** save the file, and run the last two commands

![alt tag](./images/makefile_lab/FigMakefileLab-3.png)
#### Figure 3. Output after modifying the source file

**2.1.4.** Execute the following commands to build and run the *HW (hardware) emulation* flow for the &#39;hello world&#39; example:

   ```
      make clean
      make check TARGETS=hw_emu DEVICES=$AWS_PLATFORM all
   ```

The kernel called vector\_add will be created by calling Vivado High-Level Synthesis (HLS) tool, which will try to pipeline the kernel and try to achieve initiation interval of 1.  At the end of the HLS compilation an xo file is generated.

![alt tag](./images/makefile_lab/FigMakefileLab-4.png)
#### Figure 4. HLS being used to compile the kernel

The host application will then be compiled, the xclbin and the helloworld.exe files will be generated. The application will be executed on the CPU in the hardware emulation mode showing the output and transfer rate like:

![alt tag](./images/makefile_lab/FigMakefileLab-5.png)
#### Figure 5. Execution output

## Step 3: Run the Application on F1        
### 3.1. Since the System build and AFI availability takes considerable amount of time, a precompiled version is provided. Use the precompiled solution directory to verify the functionality.
**3.1.1.** Change to the solution directory by executing the following command

   ```
      cd /home/centos/sources/makefile_flow_solution
   ```
**3.1.2.** Execute the following commands to load the AFI and execute the application to verify the functionality

   ```
      sudo sh
      source /opt/Xilinx/SDx/2017.4.rte.dyn/setup.sh
      ./helloworld xclbin/vector_addition.hw.xilinx_aws-vu9p-f1_dynamic_5_0.awsxclbin
   ```
**3.1.3.** The FPGA bitstream will be downloaded and the host application will be executed showing output something like:

![alt tag](./images/makefile_lab/FigMakefileLab-6.png)
#### Figure 6. Execution output

## Conclusion

In this lab, you used a Makefile flow to perform CPU and HW emulations. You then ran the application on F1 and validated the functionality.

---------------------------------------

<p align="center"><b>
Start the next lab: <a href="GUI_Flow_lab.md">3. GUI Flow lab</a>
</b></p>

---------------------------------------


## Appendix: Build System Hardware using Makefile Flow        

### A.1. Build the system hardware using the Makefile flow (Note that since the building of the project takes over two hours skip this step in the workshop environment and move to next step).
**A.1.1.** Execute the following commands to build the system hardware for the &#39;hello world&#39; example:

   ```
      make clean
      make check TARGETS=hw DEVICES=$AWS_PLATFORM all
   ```

This will build the project under the **System** directory. The built project will include helloword.exe file along with binary\_container\_1.xclbin file

This step takes about two hours

### A.2. Create an Amazon FPGA Image (AFI)

To execute the application on F1, the following files are needed:

- Host application (exe)
- FPGA binary (xclbin)
- Amazon FPGA Image (awsxclbin)

The xclbin and the host applications are already generated by the makefile flow

**A.2.1.** Create a **xclbin** directory under the _Makefile\_flow_ directory using the following commands

   ```
      cd /home/centos/aws-fpga/Makefile_flow
      mkdir xclbin
   ```

**A.2.2.** Copy the generated **xclbin** file ( **binary\_container\_1.xclbin** ) and the host application (helloworld) from the **helloworld\_ocl** folder into the created **xclbin** directory, using the following commands

   ```
      cd xclbin
      cp /home/centos/aws-fpga/Makefile_flow/helloworld_ocl/xclbin/vector_addition.hw.xilinx_aws-vu9p-f1_dynamic_5_0.xclbin .
      cp /home/centos/aws-fpga/Makefile_flow/helloworld_ocl/helloworld .
   ```

### A.3. Create an AFI by running the create\_sdaccel\_afi.sh script and wait for the completion of the AFI creation process
**A.3.1.** Enter the following command to generate the AFI:

   ```
      $SDACCEL_DIR/tools/create_sdaccel_afi.sh –xclbin=vector_addition.hw.xilinx_aws-vu9p-f1_dynamic_5_0.xclbin –s3_bucket=<bucket-name> -s3_dcp_key=<dcp-folder-name> -s3_logs_key=<logs-folder-name>
   ```

In the above command, &lt;bucket-name&gt;, &lt;dcp-folder-name&gt;, and &lt;logs-folder-name&gt; are the names you would have given when running CLI script.  In the workshop environment this was already done.

The create\_sdaccel\_afi.sh script does the following:

- Starts a background process to create the AFI
- Generates a \_afi\_id.txt which contains the FPGA Image Identifier (or AFI ID) and Global FPGA Image Identifier (or AGFI ID) of the generated AFIs
- Creates the \*.awsxclbin AWS FPGA binary file which will need to be read by the host application to determine which AFI should be loaded in the FPGA.

**A.3.2.** Enter the following command to note the values of the AFI IDs by opening the *\_afi\_id.txt file.

   ```
      cat *afi_id.txt
   ```

**A.3.3.** Enter the **describe-fpga-images** API command to check the status of the AFI generation process:

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
**A.3.4.** Wait until the AFI becomes available before proceeding to execute on the F1 instance.
