# Using RTL Kernel Wizard

## Introduction

This lab guides you through the steps involved in using a RTL Kernel wizard to wrap a user RTL-based IP so the generated IP can be used in SDAccel project.

## Objectives

After completing this lab, you will be able to:

- Understand how to use the RTL Kernel wizard available in SDAccel development tools
- Add the created IP in an application
- Verify functionality in hardware on F1

## Procedure

This lab is separated into steps that consist of general overview statements that provide information on the detailed instructions that follow. Follow these detailed instructions to progress through the lab.

This lab comprises three primary steps: You will create an SDAccel project. Use RTL Kernel project creation wizard, perform hardware emulation, and verify the functionality on F1.  Appendix lists steps involved in building the full hardware.

## Step 1: Create an SDAccel Project
### 1.1. Source the SDAccel settings and create a directory called rtl\_kernel under _~/aws-fpga_. Change the directory to the newly created directory.
**1.1.1.** Execute the following commands in a terminal window to source the required environment settings:

   ```
      cd ~/aws-fpga
      source sdaccel_setup.sh
      source $XILINX_SDX/settings64.sh
      mkdir rtl_kernel
      cd rtl_kernel
   ```
### 1.2. Launch SDx, create a workspace and create a project, called _rtl\_kernel_, using the _Empty Application_ template...
**1.2.1.** Launch SDAccel by executing sdx in the terminal window

An Eclipse launcher widow will appear asking to select a directory as workspace

**1.2.2.** Click on the **Browse…** button, browse to **/home/centos/aws-fpga/rtl\_kernel** , click **OK** twice

![alt tag](./images/Fig5-1.png)
#### Figure 1. Selecting a workspace

**1.2.3.** Click on the **Add Custom Platform** on the _Welcome_ page

**1.2.4.** Click on the **Add Custom Platform** button, browse to **/home/centos/aws-fpga/SDAccel/aws\_platfom/xilinx\_aws-vu9p-f1\_4ddr-xpr-2pr\_4\_0** , and click **OK**

![alt tag](./images/Fig5-2.png)
#### Figure 2. Hardware platform selected

**1.2.5.** Click **Apply** and then click **OK**

**1.2.6.** Click on the **Create SDx Project** on the _Welcome_ page

**1.2.7.** In the _New Project_&#39;s page enter **rtl\_kernel\_example** in the _Project name:_ field and click **Next**

Note the AWS-VU9P-F1 board is displayed as the hardware platform

**1.2.8.** Click **Next**

**1.2.9.** Click **Next** with Linux on x86 as the System Configuration and OpenCL as the Runtime options

**1.2.10.** Select **Empty Application** from the _Available Templates_ pane and click **Finish**

![alt tag](./images/Fig5-3.png)
#### Figure 3. Selecting an application template

## Step 2: Create RTL\_Kernel Project and Perform HW Emulation      
### 2.1. Run RTL Kernel wizard from the SDAccel project.
**2.1.1.** Make sure the **project.sdx** under _rtl\_kernel\_example_ in the **Project Explorer** tab is selected

**2.1.2.** Select **Xilinx &gt; Create RTL Kernel…**

Note that the Create RTL Kernel Wizard will be invoked displaying the Welcome screen

![alt tag](./images/Fig5-4.png)
#### Figure 4. Welcome screen of the RTL Kernel Wizard

**2.1.3.** Click **Next**

**2.1.4.** Change _Kernel_ name to **KVAdd** , (for Kernel Vector Addition), _Kernel vendor_ to **Xilinx** leaving the _Kernel library_ and _Number of clocks_ to the default values

![alt tag](./images/Fig5-5.png)
#### Figure 5. Setting general settings including name and number of clocks

**2.1.5.** Click **Next**

**2.1.6.** Click **Next** with _Number of scalar kernel input arguments_ default value being **1** and the _Argument type_ as **unit**

![alt tag](./images/Fig5-6.png)
#### Figure 6. Selecting number of scalar arguments

**2.1.7.** We will have three arguments to the kernel (2 input and 1 output) which will be passed through Global Memory. Set _Number of AXI master interfaces_ to be **3**

**2.1.8.** Keep the width of each AXI master data width to **64** (note this is specified in bytes so this will give a width of 512 bits), name **A** as the argument name to m00\_axi, **B** to m01\_axi, and **Res** to m02\_axi

![alt tag](./images/Fig5-7.png)
#### Figure 7. Selecting number of AXI master interfaces, their widths, and naming them

**2.1.9.** Click **Next** and the summary page will be displayed showing a function prototype and register map

Note the control register is accessed via S\_AXI\_CONTROL interface and is at offset 0 and the scalar operand is at offset 0x10.  There are three master AXI interfaces being used.

![alt tag](./images/Fig5-8.png)
#### Figure 8. Summary of the design interface that will be created by the wizard

**2.1.10.** Click **OK** to close the wizard

Notice that a Vivado Project will be created and opened

![alt tag](./images/Fig5-9.png)
#### Figure 9. Vivado project created by the wizard

### 2.2. Analyze the design built by the RTL kernel wizard
**2.2.1.** Expand the hierarchy of the Design Sources in the Sources window and notice all the design sources, constraint file, and the basic test bench generated by the wizard

![alt tag](./images/Fig5-10.png)
#### Figure 10. Design hierarchy along with constraints and testbench files

There is one module to handle the control signals ap\_start, ap\_done, and ap\_idle; and three master AXI channels to read source operands from and write the result back to DRAM.

Expanded m02\_axi module shows read, write, fifo\_valid\_pipeline, and an adder modules.

**2.2.2.** Select **Flow Navigator &gt; RTL ANALYSIS &gt; Open Elaborated Design** which will analyze the design and open a schematic view. Click **OK**

**2.2.3.** You should see two top-level blocks: example and control as seen below

![alt tag](./images/Fig5-11.png)
#### Figure 11. Top-level modules

**2.2.4.** Double-click on the example block and observe the three hierarchical Master AXI blocks

![alt tag](./images/Fig5-12.png)
#### Figure 12. Three master axi modules

**2.2.5.** Zoom in into the top section and see the control logic the wizard has generated to provide ap\_start, ap\_idle, and ap\_done signals

![alt tag](./images/Fig5-13.png)
#### Figure 13. Control logic generation

**2.2.6.** Traverse through one of the AXI interface blocks (m02) and observe that the example code it has generated consists of Read Master, Write Master, Read FIFO, Write FIFO, Read FIFO valid pipeline and an Adder

![alt tag](./images/Fig5-14.png)
#### Figure 14. A typical master axi hierarchical design generated by the wizard

**2.2.7.** Close the elaborated view by selecting **File &gt; Close Elaborated Design**

**2.2.8.** Click **OK**

**2.2.9.** Click on the **Generate RTL Kernel** button

The packager will be run, generating the xo file which will be used in the design

 ![](data:image/*;base64,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)

**2.2.10.** Close the Vivado project by selecting **File &gt; Exit**. Click **OK** to close.
### 2.3. Analyze the created files and added to the SDAccel project after the RTL kernel has been generated.
**2.3.1.** Expand the _src_ folder under the **rtl\_kernel\_example**

Notice that _sdx\_rtl\_kernel\_wizard_ folder and its hierarchy has been added as the source, under which a Vivado project related folders/files are included (name starting with sdx\_rtl\_kernel…) and with wizard created KVAdd\_ex folder.

![alt tag](./images/Fig5-15.png)
#### Figure 15. The rtl kernel related files added to the src folder

**2.3.2.** Expand the _KVAdd\_ex_ folder

Notice the _imports_ folder has all the files used by the rtl\_kernel

![alt tag](./images/Fig5-16.png)
#### Figure 16. RTL source files generated by the wizard and added in the project

**2.3.3.** Expand the _sdx\_imports_ folder under the _KVAdd\_ex_ folder, and notice the **main.c** and **KVAdd.xo** files. The **\*.xo** file is used during the hardware build and **main.c** has the test application generated by the wizard

![alt tag](./images/Fig5-17.png)
#### Figure 17. The application and the xo file having the IP

**2.3.4.** Double-click on the **main.c** and look at its content

The _main_ function is defined on line 59. The number of words it transfers is 4096. Lines 96 to 107 fills the source operands and expected result. Lines 200-243 deals with loading xclbin and creating kernel. Lines 248-254 creates the buffers in the device memory. Lines 294-318 sets arguments, executes the kernel, and waits for it to finish. Lines 321-360 reads the data and compares them. Lines 366-383 releases the memory, program, and kernel.

### 2.4. Add the binary container, select the Emulation-HW build configuration, and build the project.
**2.4.1.** Click on the **Add Binary Container** button ( ![](data:image/*;base64,iVBORw0KGgoAAAANSUhEUgAAALQAAAAbCAIAAABa5TJeAAAAAXNSR0IArs4c6QAAAAlwSFlzAAASdAAAEnQB3mYfeAAACItJREFUaEPtWglQE2cU3g0R5NCC7ShovbVVR+Xo2PGIR1GKjSIgYEXwAEQUtSIURYLY6IC1iICckRFGUUQNBK1FrR2P1qvjMTIy1VFmtFoJClIQ1KIh27f5wxI22WQ3ZhxhsvNm59//f//3v/f22/+97AZvfKXAzIc5AroigDeZyWFmBkMEeObImCPAFAH8xWtzWjHTQ3cE8OZuQY5X/rNNeIdtpKdMiNZ1oXh41zmePq1lMta0N6DrhKSTpXriY5xH5prDSF7hOGaBYz14hJUF0ZPfIZYWhEUXCWr1/VsN9bVNTXWMNcfL1jYjw/Pep9XWyh0dnXQu2zLfE/XblZ1mY1fKdovYTXTH2YMAM4AAPJzgQeNaDNZwCxbdeu3CluH2Cq/rStuhCiUbK0ysoyc+Old6+/ZNwR5/P7+ce9XnJk4O4vHopMZfaZCjvOKKfnt9hJO4OiQ7dVn/FN/Zk1li6nG+uZ0cvVTkUNTfab1bpnh+Vyey5K/iLVvOi8UzosOr+J+MpnRoIIzPE47hFDMuheHV+0BT/BhDmCKRoo3AlQRLn0i1gxmpQ93cKosLV+Xu15yWu2qJ86KQBzdvBq2LYQPHlRwKhaL+WY1MFhLgv7+iIt7HN723vYPmQlBzYJTAwOfTpgycrFtgVFOZZRtmubgLmIQbJrMBlEvIKv4Dma0V/lH/0doCzKCUd+WPpbmPhvS4Bk8X7BYWPNWeocEMCjMpiY9hBMvggBowY4ira9U28fjAZcAGaiK0xy1cCv2DXVxAhxUgxxvUowf//Pmc+b6FR6WLPTzEhYVzXr1s7rTQ6zcdu6vslyvAjKulUv08XbN8PhsiI52yk5eBGRVFJsCslcsdnXSnlRe+6rTSW0buHHcL/HVaeKxWBs+35hA86xvj1RGggWgjwIYBhzqbdN4zmDANBurRg+pDYcE+Wfm30nY6BgbfzEvz27G7dON3bivXy4sPukbHlK8JD9x7YNDQEQah9MSHaW7dU3l5eegcYW6pbIlwtqSm9vbUaQsoZT7Usdoz3b/1Z3pxCtTROcWg6cLFum8YTATqsMFEJbf+hfQreDv6AhsAAaUAaMSJdFQHTCDkU4URZLXRzgxASHR2ZoPJZPbgYSO9MvJkK0LnZktu/5QiiBYVL1k4MyXjfmbW+A2xsojQeTn5oGMwvKDAJj40nPvVV73n5R+RBvvMK9x/wDs46Lim77A7khkUCXUAMwbaYgNs6PKpLamiOcVgW9OaflYYkrF+IqoNDa6Y2otSq6ChOusvLlbV6pSP6zvKpknDtul0n8kpjCDIISgyWh4SjtPRonhD5ZaBHV7GJygNxoSmMHa868yd6cfDw0atj7q3I9UzLRvOo9dHQc/MlHQY5QrIUh+SSO9eQ8vKtn89K6v4sH+AX4lMtklzLr3moLyEisrBSoeoGMpBNMlB5mmVwPG0Vd1Gl2ww4bYzqXWQQ6UzuleNj7srk4SNj4RnHc5j7OScag4wFYwlRixVflWKWdpTiyqn7E1IUJDVaAKZodj4QtOZOFngmvjDnxGrR26IrkrYCmdoQw/0s0fTEx9tEHnNw0d/P8zOjlgZmdny8vlcYWHh/nnuX8UShJJSZv5J3n4jqTtK3Vc2W5xOHQsMQ4IO2qXRsNoT65/J9QvkF6TAZVH0C4TA3jTyTs/EHx0jr+yGKL1uAF0IDFcxw0DW07PcjdhNw5PEd+LEw0Wx5DlJDD1czOOgW1l5sb6uIT0jcEHAVqVSOeHLGSM/G7V29flDh9a2KTo+p+hOK7DOMF+R/Ry6ICqx3LVoqQomUoDQhsyCLpFPnDANphX2cWKfVlS+tzOjoZK8cJze5nWd6OPczgmSGcY5kuYdMC5J/ES8c5g47t4GMZyhDT3QbxygnllnThcRbT1T0xYF+KXk5EbxLeBlHmZjbW1j3SMu7uej0uQXTY3P655AJ2NaeSBLaq5Iui/tJOi5YL/R0crHlyeTqkpJARDUAPnnP86Y2gZQbEBD7A+2aQVoAY6jPUPFDHRAZQobiZoTXLKt5rq7vAMGBvn9m5IzfHvC9ci1vaMj4AzthpRs6IdRTgE3qOzgMDIjc0nworSc3IjQkB+VyjaQ3y+UWlpan/w1WTh7zRHpmtbWtn1FUYxpRUUcrL9tJ0H1gdEH5JERNqSgA7WpS6Nh38fEdscJ50R1tQE1R7/pUG1glg6q7cLI0Ehi4gYE+jal5gEbzi4KFxTkN16rhDO0R2zf3JiaC6OgY0IfCwojFwel50oiwpdn7cmPbWlpLjmU6OY2S3Zsk9Az/nDpaqFncol0mWBSZKcPb2DB48uXkB3gLK3aoGoOTl9xAOrW2YtqTB6Gq0T7khOmtjIVO64/5zShaCCdVlFVomRYKrfCVkGMWdfmV610ScSsHFRqxn+/9IldVxUlctocDWwQlhVNEUxduWsHnKENPf03x8Ao6LxjfDSnB6uYERaSmb57VXKy9MKF8okTl+XkLZjlHn+gJGSO546iksBvPDKOliZ2qjkWek8BoWJE/qDXEvUWyjq5BnoJQBAmhfb2NJlZqEuumO+/5lDbD0mkjwtJiwmpOKIFernAOhramk5O/ddWXr66fB2wQSCYSilAG3qgH0ZB512WoM2V7FmxdHFaZlZUvuSPcWPHzfcNGzRocER4yZnf9s0QbMuSCIUeWU9q7sZtKMYJgv4ZYGeugbeZ369ifJ3FtPuZBFMulzsxvCF9NtcDLd33xBmjd2A9IBAlouYcIEMRil4Tcd2ijLaK/UQ98dEEOXHiRJ5EVLD3TN++fWngzc3Nra2tfD7f3t6e9FGbHOyt+XA03wc52p8iihwfjvucLAkJFWbuPmJnZ2dwVncjh0GH2Si8y/bDBr+r6HSR/6V0lXB2LzvN5Ohe99Ok3nSTtGLSmJjB1BEw7xxmKjBG4H9sh2WAFsJdvwAAAABJRU5ErkJggg==))

Notice the _binary\_container\_1_ is added to the project. Since the design has RTL IP, the binary container does not have further hierarchy

![alt tag](./images/Fig5-18.png)
#### Figure 18. Adding binary container to the project

**2.4.2.** Either select **Project &gt; Build Configurations &gt; Set Active &gt; Emulation-HW** or click on the drop-down button of _Active build configuration_ and select **Emulation-HW**

![alt tag](./images/Fig5-19.png)
#### Figure 19. Selecting HW emulation build configuration

**2.4.3.** Either select **Project &gt; Build Project** or click on the ![](data:image/*;base64,iVBORw0KGgoAAAANSUhEUgAAABoAAAAbCAIAAADtdAg8AAAAAXNSR0IArs4c6QAAAAlwSFlzAAASdAAAEnQB3mYfeAAAAqVJREFUSEutlM9P2mAYx1t+CLX1belLSyiLceoCGSroZQezwxI9zNuyZMd5cIedx9+y2zy4qy4cdjJZotl2WZawCO4ApWaRFlhx1JafFQHfxcQMcMCWvnkPb94fn+f7ft/nffBCIY9Z12zWoX6TbN0uZmG3Wh2GdS3seD6v3Grf9pvtVCr159Li4uLWi63hXt+C03V9d3cvk06vrKzMzs1dnz+RpEQiEV5Y2Nx8PoTYj2u1Wjs7b2047hcEn8+XTqezoojOm6ZJM8zGxuNwOPwPuORRMh6Pr62vAwCq1WoymcQxLBKNQsgSBOH1el0u1xBcz8siv/b39zmOc7vdiFWpVNCg3WribXN+fj4QCAxn9ecd8h6xkF/XrEajUa/VNCXzI/FByabGSc/+vENmMwwj53KSJImiWNdkn1PvFI+OD3fz2eTIL9SDEwRBzGQMw9A07bJ10VSzsKuGpgE3hZ2ffP1+uJeXelJnkG6PxWI3s3a7/fDgoNlsopsGQyE/1SG7OksTgCI7zYr2Uzb08iTNT7H832Qi3KubNZ7ndd2o1aoA0NHl5dl7ITUnNnQV8vyE09Yxq+ViTtdKU1CYBB4cR2/e33pwSF04fB+ybCQaQe8IPFxZVc6UE4JweVgP3r3ELupa8VT7VaJYgaRZ3NZvfQ8OxUIxIYQURSE0GjP8nZqhGyWZhXDC6ei0TCTyvHh6VlI9wixJwz55IwrUJIAzSw+ddKBQKBEMR0EvSVJe4NKlL8ef3g+mzugCxU8HKe+MIufrZhtwftLD2h1Op8N2rsqD3o3GocN3l1Z9wQdSVnIQtBuwnQlXudaOPHo6iMNl+ZYgg/sU8ejzu9fSt4/IXQD9y2vPVp+8/H/cyP9wvWH0ZccE3eCsLO5Wqxun7Iy/x2J1V1Re5zUBRl+EAAAAAElFTkSuQmCC) button

This will build the project including rtl\_kernel\_example.exe file under the Emulation-HW directory

**2.4.4.** Select **Run &gt; Run Configurations…** to open the configurations window

**2.4.5.** Click on the **Arguments** tab and notice that no binary container is assigned

![alt tag](./images/Fig5-20.png)
#### Figure 20. Unpopulated Arguments tab

**2.4.6.** Click on the **Automatically add binary container(s) to arguments** check box, click **Apply** , and the click **Run** to run the application

![alt tag](./images/Fig5-21.png)
#### Figure 21. Program argument assigned

**2.4.7.** The Console tab shows that the test was completed successfully along with the data transfer rate

![alt tag](./images/Fig5-22.png)
#### Figure 22. Hardware emulation run output

**2.4.8.** Double-click on the **Application Timeline** entry in the _Reports_ tab, expand all entries in the timeline graph, zoom appropriately and observe the transactions

![alt tag](./images/Fig5-23.png)
#### Figure 23. Timeline graph showing various activities in various region of the system

## Step 3: Run the Application on F1        
### 3.1. Since the System build and AFI availability takes considerable amount of time, a precompiled version is provided. Use the precompiled solution directory to verify the functionality.
**3.1.1.** Change to the solution directory by executing the following command

   ```
      cd /home/centos/sources/rtl_kernel_solution
   ```
**3.1.2.** Run the following commands to load the AFI and execute the application to verify the functionality

   ```
      sudo sh
      source /opt/Xilinx/SDx/2017.1.rte/setup.sh
      ./rtl_kernel_example.exe xclbin/binary_container_1.awsxclbin
   ```

**3.1.3.** The FPGA bitstream will be downloaded and the host application will be executed showing output something like:

![alt tag](./images/Fig5-24.png)
#### Figure 24. Execution output

**3.1.4.** Enter exit in the teminal window to exit out of sudo shell

**3.1.5.** Close the SDx by selecting **File &gt; Exit**

## Conclusion

In this lab, you used the RTL Kernel wizard to create a sample adder application. You saw that the wizard creates an RTL IP with the specified number of AXI master ports. You performed HW emulation and analyzed the application timeline. You finally ran the application on an AWS F1 instance and validated the functionality.

## Appendix: Build Full Hardware 
### A.1. Set the build configuration to System and build the system (Note that since the building of the project takes over two hours skip this step in the workshop environment and move to next step).
**A.1.1.** Either select **Project &gt; Build Configurations &gt; Set Active &gt; System** r click on the drop-down button of _Active build configuration_ and select **System**

**A.1.2.** Either select **Project &gt; Build Project** or click on the ![](data:image/*;base64,iVBORw0KGgoAAAANSUhEUgAAABoAAAAbCAIAAADtdAg8AAAAAXNSR0IArs4c6QAAAAlwSFlzAAASdAAAEnQB3mYfeAAAAqVJREFUSEutlM9P2mAYx1t+CLX1belLSyiLceoCGSroZQezwxI9zNuyZMd5cIedx9+y2zy4qy4cdjJZotl2WZawCO4ApWaRFlhx1JafFQHfxcQMcMCWvnkPb94fn+f7ft/nffBCIY9Z12zWoX6TbN0uZmG3Wh2GdS3seD6v3Grf9pvtVCr159Li4uLWi63hXt+C03V9d3cvk06vrKzMzs1dnz+RpEQiEV5Y2Nx8PoTYj2u1Wjs7b2047hcEn8+XTqezoojOm6ZJM8zGxuNwOPwPuORRMh6Pr62vAwCq1WoymcQxLBKNQsgSBOH1el0u1xBcz8siv/b39zmOc7vdiFWpVNCg3WribXN+fj4QCAxn9ecd8h6xkF/XrEajUa/VNCXzI/FByabGSc/+vENmMwwj53KSJImiWNdkn1PvFI+OD3fz2eTIL9SDEwRBzGQMw9A07bJ10VSzsKuGpgE3hZ2ffP1+uJeXelJnkG6PxWI3s3a7/fDgoNlsopsGQyE/1SG7OksTgCI7zYr2Uzb08iTNT7H832Qi3KubNZ7ndd2o1aoA0NHl5dl7ITUnNnQV8vyE09Yxq+ViTtdKU1CYBB4cR2/e33pwSF04fB+ybCQaQe8IPFxZVc6UE4JweVgP3r3ELupa8VT7VaJYgaRZ3NZvfQ8OxUIxIYQURSE0GjP8nZqhGyWZhXDC6ei0TCTyvHh6VlI9wixJwz55IwrUJIAzSw+ddKBQKBEMR0EvSVJe4NKlL8ef3g+mzugCxU8HKe+MIufrZhtwftLD2h1Op8N2rsqD3o3GocN3l1Z9wQdSVnIQtBuwnQlXudaOPHo6iMNl+ZYgg/sU8ejzu9fSt4/IXQD9y2vPVp+8/H/cyP9wvWH0ZccE3eCsLO5Wqxun7Iy/x2J1V1Re5zUBRl+EAAAAAElFTkSuQmCC) button

This will build the project under the **System** directory. The built project will include rtl\_kernel\_example.exe file along with binary\_container\_1.xclbin file

This step takes about two hours

### A.2. Create an Amazon FPGA Image (AFI)

To execute the application on F1, the following files are needed:

- Host application
- FPGA binary (xclbin)
- Amazon FPGA Image (awsxclbin)

The xclbin and the host applications are already generated by the System configuration step

**A.2.1.** Create a **xclbin** directory under the _rtl\_kernel\_example_ directory using the File Explorer

   ```
      cd /home/centos/aws-fpga/rtl_kernel
      mkdir xclbin
   ```
**A.2.2.** Copy the generated **xclbin** file ( **binary\_container\_1.xclbin** ) and the host application (rtl\_kernel\_example.exe) from the **System** folder into the created **xclbin** directory, using the following commands

   ```
      cd xclbin
      cp /home/centos/aws-fpga/rtl_kernel/rtl_kernel_example/System/binary_container_1.xclbin .
      cp /home/centos/aws-fpga/rtl_kernel/rtl_kernel_example/System/rtl_kernel_example.exe .
   ```
### A.3. Create an AFI by running the create\_sdaccel\_afi.sh script and wait for the completion of the AFI creation process
**A.3.1.** Enter the following command to generate the AFI:

   ```
      $SDACCEL_DIR/tools/create_sdaccel_afi.sh –xclbin=binary_container_1.xclbin –s3_bucket=<bucket-name> -s3_dcp_key=<dcp-folder-name> -s3_logs_key=<logs-folder-name>
   ```
In the above command, &lt;bucket-name&gt;, &lt;dcp-folder-name&gt;, and &lt;logs-folder-name&gt; are the names you would have given when running CLI script.  In the workshop environment this was already done.

The create\_sdaccel\_afi.sh script does the following:

- Starts a background process to create the AFI
- Generates a \_afi\_id.txt which contains the FPGA Image Identifier (or AFI ID) and Global FPGA Image Identifier (or AGFI ID) of the generated AFIs
- Creates the \*.awsxclbin AWS FPGA binary file which will need to be read by the host application to determine which AFI should be loaded in the FPGA.

**A.3.2.** Enter the following command to note the values of the AFI IDs by opening the \_afi\_id.txt file.

   ```
      cat *afi_id.txt
   ```
**A.3.3.** Enter the **describe-fpga-images** API command to check the status of the AFI generation process:

**aws ec2 describe-fpga-images --fpga-image-ids &lt;AFI ID&gt;**

Note: When AFI creation completes successfully, the output should contain:

   ```
      ...
      "State": {
          "Code": "available"
      },
      
      ...
   ```

**A.3.4.** Wait until the AFI becomes available before proceeding to execute on the F1 instance.

