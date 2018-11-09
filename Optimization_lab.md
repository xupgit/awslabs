# Optimization Lab

## Introduction

This lab guides you through the steps involved in creating a project and adding a kernel function. After creating a project you will run software and hardware emulations to verify the functionality, analyze various generated reports and then apply optimization techniques both on host and kernel side to improve throughput and data transfer rate.

## Objectives

After completing this lab, you will be able to:

- Add a function as a kernel
- Analyze and understand various generated reports
- Optimize kernel code to improve throughput
- Optimize host code to improve data transfer rate
- Verify functionality in hardware on F1

## Steps 
### Create an SDAccel Project
1. Execute the following commands, if it is not already done, in a terminal window to source the required Xilinx tools:
   ```
      cd ~/aws-fpga		  
      source sdaccel_setup.sh		  
      source $XILINX_SDX/settings64.sh	  
   ```
1. Execute the following commands in a terminal window to create a working directory:
   ```
      mkdir optimization_flow	  
      cd optimization_flow
   ```
1. Launch SDAccel by executing **sdx** in the terminal window  
An Eclipse launcher window will appear asking to select a directory as workspace
1. Click on the **Browse…** button, browse to **/home/centos/src/project_data/aws-fpga/optimization\_lab**, click **OK** twice  
    <p align="center">
    <img src ="./images/workspace.png"/>
    </p>
    <p align = "center">
    <i>Selecting a workspace</i>
    </p>
    The Xilinx SDx IDE window will be displayed
    <p align="center">
    <img src ="./images/SDX_IDE.png"/>
    </p>
    <p align = "center">
    <i>The SDx IDE window</i>
    </p>
1. Click on the **Add Custom Platform** link on the _Welcome_ page
1. Click on the **Add Custom Platform** button, browse to **/home/centos/aws-fpga/SDAccel/aws\_platfom/xilinx\_aws-vu9p-f1-04261818\_dynamic\_5\_0**, and click **OK**
    <p align="center">
    <img src ="./images/FigPlatform.png"/>
    </p>
    <p align = "center">
    <i>Hardware platform selected</i>
    </p>
1. Click **Apply** and then click **OK**
1. Click on the **Create SDx Project** link on the _Welcome_ page
1. Click **Next** 
1. In the _New Project_&#39;s page enter **optimization\_lab\_example** in the _Project name:_ field and click **Next**   
Note the aws-vu9p-f1-04261818 board is displayed as the hardware platform
1. Click **Next**
1. Click **Next** with Linux on x86 as the System Configuration and OpenCL as the Runtime options
1. Select **Empty Application** from the _Available Templates_ pane and click **Finish**
    <p align="center">
    <img src ="./images/FigEmptyProjectTemplate.png"/>
    </p>
    <p align = "center">
    <i>Selecting an application template</i>
    </p>
The project IDE will be displayed with six main windows: Project Explorer, Project Settings, Reports, Outline, multi-tab console, and Emulation Console.
    <p align="center">
    <img src ="./images/optimization_lab/FigOptimizationLab-5.png"/>
    </p>
    <p align = "center">
    <i>The Project IDE</i>
    </p>

### Import the provided two source files from the /home/centos/sources/optimization\_lab folder to the project
1. Right-click on the **src** folder in the _Project Explorer_ and select **Import…**
1. Select **General &gt; File System**, click **Next**, browse to the source directory at **/home/centos/sources/optimization\_lab** and click **OK**
1. Select the **idct.cpp** and **krnl\_idct.cpp** files
1. Click **Finish**
1. Expand the **src** folder in the _Project Explorer_ and note the two added files  
### Select the function(s) that needs to be accelerated
1. Click on the _Add Hardware Function_ button icon (![alt tag](./images/Fig-hw_button.png)) in the **Hardware Functions** tab to see possible functions which may be accelerated  
1. Select _kernl\_idct_ function and click **OK**
    <p align="center">
    <img src ="./images/optimization_lab/FigOptimizationLab-6.png"/>
    </p>
    <p align = "center">
    <i>Selecting a kernel function</i>
    </p>
1. Notice the **binary\_container\_1** folder is created under which _kml\_idct_ function is added
### Analyze the source files  
1. Open the **krnl\_idct.cpp** file  
1. Locate the **Outline** viewer corresponds to a function in the selected source file. This view provides a convenient way of looking up and navigating the code hierarchy. Each green dot in the **Outline** viewer corresponds to a function in the selected source file  
    <p align="center">
    <img src ="./images/optimization_lab/FigOptimizationLab-7.png"/>
    </p>
    <p align = "center">
    <i>Outline view</i>
    </p>
1. In the _Outline_ viewer, click **idct** to look up the function  
The idct function is the core algorithm implemented in the custom hardware accelerator. It is a computationally intensive function that can be highly paralleled on the FPGA, providing significant acceleration over a CPU-based implementation.  

1. Review other functions of the accelerator  

    - **krnl\_idct** : This is the top-level for the custom hardware accelerator. Interface properties for the accelerator are specified in this function
    - **krnl\_idct\_dataflow** : This function is called by the **krnl\_idct** function and encapsulates the main functions of the accelerator
    - **read\_blocks** : This function reads from global memory values sent by the host application and streams them to the **execute** function
    - **execute** : This function receives the streaming data and, for each 8x8 block received, calls the **idct** function to perform the actual computation and streams the results back out
    - **write\_blocks** : This function receives the streaming results from the **execute** function and writes them back to global memory for the host application  
5. Open the **idct.cpp** file.  Again use the _Outline_ viewer to quickly look up and inspect the important functions of the host application:  
    - **main** : Initializes the test vectors, sets-up OpenCL resources, runs the reference model, runs the hardware accelerator, releases the OpenCL resources, and compares the results of the reference IDCT model with the accelerator implementation
    - **runFPGA** : This function takes in a vector of inputs and, for each 8x8 block, calls the hardware accelerated IDCT using the **write** , **run** , **read** , and **finish** helper functions. These function use OpenCL API calls to communicate with the FPGA
    - **runCPU** : This function takes in a vector of inputs and, for each 8x8 block, calls **idctSoft** , a reference implementation of the IDCT
    - **idctSoft** : This function is the reference software implementation of the IDCT algorithm, used in this example to check the results coming back from the FPGA
    - **oclDct** : This class is used to encapsulate the OpenCL runtime calls to interact with the kernel in the FPGA
    - **aligned\_allocator** , **smalloc** , **load\_file\_to\_memory** , **getBinaryName** : These are small helper functions used during test vector generation and OpenCL setup  
6. Go to line near line no. 580 of the **idct.cpp** file by pressing Ctrl+l (small L) and entering 580  
This section of code is where the OpenCL environment is setup in the host application. It is typical of most SDAccel application and will look very familiar to developers with prior OpenCL experience. This body of code can often be reused as-is from project to project.  
To setup the OpenCL environment, the following API calls are made:    
    - **clGetPlatformIDs** : This function queries the system to identify the any available OpenCL platforms. It is called twice as it first extracts the number of platforms before extracting the actual supported platforms
    - **clGetPlatformInfo** : Get specific information about the OpenCL platform, such as vendor name and platform name
    - **clGetDeviceIDs** : Obtain list of devices available on a platform
    - **clCreateContext** : Creates an OpenCL context, which manages the runtime objects
    - **clGetDeviceInfo** : Get information about an OpenCL device like the device name
    - **clCreateProgramWithBinary** : Creates a program object for a context, and loads specified binary data into the program object. The actual program is obtained before this call through the load\_file\_to memory function
    - **clCreateKernel** : Creates a kernel object
    - **clCreateCommandQueue** : Create a command-queue on a specific device  
Note: all objects accessed through a **clCreate..**. function call should be released before terminating the program by calling **clRelease...**. This avoids memory leakage and clears the locks on the device

### Set the XOCC Kernel Linker flags

**In the idct.cpp file, locate lines 308-310 and note that there are two DDR banks (BANK0 and BANK1) are being used. By default, the compiler will connect all m\_axi ports to DDR BANK0. In order to instruct the compiler that BANK1 is available, the XOCC Kernel Linker flag has to be added. Add --sp krnl\_idct_1.m\_axi\_gmem:bank0 --sp krnl\_idct\_1.m\_axi\_gmem1:bank0 --sp krnl\_idct\_1.m\_axi\_gmem2:bank1 in the linker flag field**

1. In the Project Explorer pane, right-click the project **optimization\_lab\_example** and select the **C/C++ Settings**
1. Select **C/C++ Build** &gt; **Settings** in the left pane
1. Select the **Miscellaneous** under **SDx XOCC Kernel Linker**
1. Using the gedit editor, open the file **xocc\_linker\_flag.txt** from the **/home/centos/sources/optimization\_lab/** directory, copy --sp krnl\_idct_1.m\_axi\_gmem:bank0 --sp krnl\_idct\_1.m\_axi\_gmem1:bank0 --sp krnl\_idct\_1.m\_axi\_gmem2:bank1 and paste it in the **Other flags** field. Make sure that there no control characters at the end of the string
    <p align="center">
    <img src ="./images/optimization_lab/FigOptimizationLab-8.png"/>
    </p>
    <p align = "center">
    <i>Adding the XOCC Kernel Linker flag</i>
    </p>
1. Click **OK**
### Build and run software emulation (Emulation-SW)
1. Make sure the **project.sdx** under _Optimization\_lab\_example_ in the **Project Explorer** tab is selected
1. Either select **Project &gt; Build Configurations &gt; Set Active &gt; Emulation-SW** or click on the drop-down button of _Active build configuration_ and select **Emulation-SW**
1. Either select **Project &gt; Build Project** or click on the (![alt tag](./images/Fig-build.png)) button  
This will build the project including Optimization\_lab\_example.exe file under the Emulation-SW directory
1. In the Project Explorer pane, right-click the project **optimization\_lab\_example** and select **Run As** &gt; **Run Configurations…**
1. Select the **Arguments** tab
1. Click on the **Automatically add binary container(s) to arguments** check box  
This will add **../binary\_container\_1.xclbin**
1. Click on the **Environment** tab and change the _LD\_LIBRARY\_PATH_ to **/opt/xilinx/xrt/lib** and click **OK**
1. Click **Apply** and then click **Run**  
The application will be run and the output will be displayed in the Console tab
    <p align="center">
    <img src ="./images/optimization_lab/FigOptimizationLab-10.png"/>
    </p>
    <p align = "center">
    <i>SW Emulation run output</i>
    </p>

### Review the software emulation reports
1. In the **Assistant** tab, expand **optimization\_lab\_example** &gt; **Emulation-SW** &gt; **optimization\_lab\_example-Default**  
There will be two files generated by the tool after running the software emulation: Profile Summary and Application Timeline
    <p align="center">
    <img src ="./images/optimization_lab/FigOptimizationLab-11.png"/>
    </p>
    <p align = "center">
    <i>Generated reports</i>
    </p>
1. Double-click the **Profile Summary** report and review it  
This report provides data related to how the application runs. Notice that the report has four tabs at the top: **Top Operations**, **Kernels &amp; Compute Units**, **Data Transfers**, and **OpenCL APIs**.
    <p align="center">
    <img src ="./images/optimization_lab/FigOptimizationLab-12.png"/>
    </p>
    <p align = "center">
    <i>The Profile Summary report</i>
    </p>  
1. Click on each of tabs and review the report:  
    - **Top Operations** : Shows all the major top operations of memory transfer between the host and kernel to global memory, and kernel execution. This allows you to identify throughput bottlenecks when transferring data. Efficient transfer of data to the kernel/host allows for faster execution times
    - **Kernels &amp; Compute Units** : Shows the number of times the kernel was executed. Includes the total, minimum, average, and maximum run times. If the design has multiple compute units, it will show each compute unit&#39;s utilization. When accelerating an algorithm, the faster the kernel executes, the higher the throughput which can be achieved. It is best to optimize the kernel to be as fast as it can be with the data it requires
    - **Data Transfers** : This tab has no bearing in software emulation as no actual data transfers are emulated across the host to the platform. In hardware emulation, this shows the throughput and bandwidth of the read/writes to the global memory that the host and kernel share
    - **OpenCL APIs** : Shows all the OpenCL API command executions, how many time each was executed, and how long they take to execute
1. Double-click the **Application Timeline** report and review it
    <p align="center">
    <img src ="./images/optimization_lab/FigOptimizationLab-13.png"/>
    </p>
    <p align = "center">
    <i>The Application Timeline</i>
    </p>  
The **Application Timeline** collects and displays host and device events on a common timeline to help you understand and visualize the overall health and performance of your systems. These events include OpenCL API calls from the host code: when they happen and how long each of them takes.

### Perform HW Emulation      
1. Either select **Project &gt; Build Configurations &gt; Set Active &gt; Emulation-HW** or click on the drop-down button of _Active build configuration_ and select **Emulation-HW**
1. Set the XOCC Kernel Linker flag as done earlier for the Emulation-SW mode 
1. Either select **Project &gt; Build Project** or click on the (![alt tag](./images/Fig-build.png)) button  
This will build the project including Optimization\_lab\_example.exe file under the Emulation-HW directory
1. Select **Run &gt; Run Configurations…** to open the configurations window
1. In the **Main** tab, click on the _Use waveform for kernel debugging_ and then _Launch live waveform_ options
    <p align="center">
    <img src ="./images/optimization_lab/FigOptimizationLab-14.png"/>
    </p>
    <p align = "center">
    <i>Setting up for live waveform</i>
    </p>  
1. Click on the **Arguments** tab and notice that the _binary\_container\_1.xclbin_ is already assigned  
If no argument was assigned then you would have to explicitly assign the **xclbin** by clicking on the _Automatically add binary container(s) to arguments_, and click **Apply**
1. Click **Run** to run the application  
1. The Console tab shows that the test was completed successfully along with the data transfer rate  
    <p align="center">
    <img src ="./images/optimization_lab/FigOptimizationLab-15.png"/>
    </p>
    <p align = "center">
    <i>Hardware emulation run output</i>
    </p>  
Notice that Vivado was started and the simulation waveform window is updated.
1. Click on the Zoom full button and scroll down the waveform window to see activites taking place in the kernel  
Since there is no optimzation is done observe that the execution is sequential
    <p align="center">
    <img src ="./images/optimization_lab/FigOptimizationLab-15-1.png"/>
    </p>
    <p align = "center">
    <i>Vivado simulator output</i>
    </p>  

### Understand the HLS Report, profile summary, and Application Timeline
1. In the **Assistant** tab, expand **optimization\_lab\_example** &gt; **Emulation-HW** &gt; **optimization\_lab\_example-Default**
1. Double-click the **Profile Summary** report and review it
    <p align="center">
    <img src ="./images/optimization_lab/FigOptimizationLab-16.png"/>
    </p>
    <p align = "center">
    <i>HW-Emulation Profile Summary report</i>
    </p>  
1. Click on the **Kernels &amp; Compute Units** tab of the Profile Summary report
1. Review the Kernel **Total Time (ms)**  
This number will serve as a baseline (reference point) to compare against after optimization.
    <p align="center">
    <img src ="./images/optimization_lab/FigOptimizationLab-17.png"/>
    </p>
    <p align = "center">
    <i>HW-Emulation Kernels &amp; Compute Units report</i>
    </p>  
1. In the **Assistant** tab, expand **optimization\_lab\_example** &gt; **Emulation-HW** &gt; **binary\_container\_1** &gt; **krnl\_idct**
1. Double-click the **HLS Report** and review it
    <p align="center">
    <img src ="./images/optimization_lab/FigOptimizationLab-18.png"/>
    </p>
    <p align = "center">
    <i>HLS report before optimization</i>
    </p>  
1. In the **Performance Estimates** section, expand the **Latency (clock cycles)** &gt; **Summary** and note the following numbers:  
    - Latency (min/max): 6185
    - Interval (min/max): 6185  
The numbers will serve as a baseline for comparison against optimized versions of the kernel
1. In the HLS report, expand **Latency (clock cycles)** &gt; **Detail** &gt; **Instance**  
    - Note that the 3 sub-functions read, execute and write have roughly the same latency and that their sum total is equivalent to the total Interval reported in the Summary table
    - This indicates that the three sub-functions are executing sequentially, hinting to an optimization opportunity
1. Close all the reports

### Analyze the kernel code and apply the DATAFLOW directive
1. Open the **src > krnl\_idct.cpp** file
1. Using the **Outline** viewer, navigate to the **krnl\_idct\_dataflow** function  
Observe that the three functions are communicating using **hls::streams** objects. These objects model a FIFO-based communication scheme. This is the recommended coding style which should be used whenever possible to exhibit streaming behavior and allow **DATAFLOW** optimization
1. Enable the DATAFLOW optimization by uncommenting the **#pragma HLS DATAFLOW** present in the krnl\_idct\_dataflow function (line 322)     - The DATAFLOW optimization allows each of the subsequent functions to execute as independent processes
    - This results in overlapping and pipelined execution of the read, execute and write functions instead of sequential execution
    - The FIFO channels between the different processes do not need to buffer the complete dataset anymore but can directly stream the data to the next block
1. Comment the three **#pragma HLS stream** statements on lines 327, 328 and 329
1. Save the file
### Build the project in Hardware emulation configuration and analyze the HLS report
1. Make sure the active configuration is **Emulation-HW**
1. Click on the Build button (![alt tag](./images/Fig-build.png)) to build the project
1. In the **Assistant** tab, expand **optimization\_lab\_example** &gt; **Emulation-HW** &gt; **binary\_container\_1**  &gt;   **krnl\_idct**
1. Double-click the **HLS Report** and review it
    <p align="center">
    <img src ="./images/optimization_lab/FigOptimizationLab-19.png"/>
    </p>
    <p align = "center">
    <i>HLS report after applying pragma DATAFLOW</i>
    </p>  
1. In the **Performance Estimates** section, expand the **Latency (clock cycles)** &gt; **Summary** and note the following numbers:
    - Latency (min/max): 2085
    - Interval (min/max): 2069
###  Run the Hardware Emulation
1. Run the application by clicking the Run button (![alt tag](./images/Fig-run.png))  
Wait for the run to finish with RUN COMPLETE message  
Notice the Vivado simulator starts, displaying various activites  
Since there is dataflow optimzation done observe concurrent execution of reading, writing, pipelining and kernel running 
    <p align="center">
    <img src ="./images/optimization_lab/FigOptimizationLab-20-1.png"/>
    </p>
    <p align = "center">
    <i>Vivado simulator output of the DATAFLOW optimized kernel</i>
    </p>  

1. In the **Assistant** tab, expand **optimization\_lab\_example > Emulation-HW > optimization\_lab\_example-Default**  and double-click the **Profile Summary** report
1. Select the **Kernels &amp; Compute Units** tab.  
Compare the **Kernel Total Time (ms)** with the results from the un-optimized run
    <p align="center">
    <img src ="./images/optimization_lab/FigOptimizationLab-20.png"/>
    </p>
    <p align = "center">
    <i>Total execution time of 0.015 compared to 0.029 of un-optimized</i>
    </p>  

### Analyze the host code
1. Open the **src > idct.cpp** file
1. Using the **Outline** viewer, navigate to the **runFPGA** function  
For each block of 8x8 values, the **runFPGA** function writes data to the FPGA, runs the kernel, and reads results back. Communication with the FPGA is handled by the OpenCL API calls made within the cu.write, cu.run and cu.read functions  
    - **clEnqueueMigrateMemObjects** schedules the transfer of data to or from the FPGA
    - **clEnqueueTask** schedules the executing of the kernel  
These OpenCL functions use events to signal their completion and synchronize execution  
1. Open the **Application Timeline** of the _Emulation-HW_ run  
The green segments at the bottom indicate when the IDCT kernel is running
    <p align="center">
    <img src ="./images/optimization_lab/FigOptimizationLab-21.png"/>
    </p>
    <p align = "center">
    <i>Application Timeline before host code optimization</i>
    </p>  
1. Notice that there are gaps between each of the green segments indicating that the operations are not overlapping
1. Zoom in by performing a left mouse drag across one of these gaps to get a more detailed view  
    - The two green segments correspond to two consecutive invocations of the IDCT kernel
    - The gap between the two segments is indicative of the kernel idle time between these two invocations
    - The **Data Transfer** section of the timeline shows that **Read** and **Write** operations are happening when the kernel is idle
    - The Read operation is to retrieve the results from the execution which just finished and the Write operation is to send inputs for the next execution
    - This represents a sequential execution flow of each iteration
1. Close the **Application Timeline**  
1. In the **idct.cpp** file, go to the **oclDct::write** function  
    - Observe that on line 353, the function synchronizes on the **outEvVec** event through a call to **clWaitForEvents**
    - This event is generated by the completion of the **clEnqueueMigrateMemObjects** call in the **oclDct::read** function (line 429)
    - Effectively the next execution of the **oclDct::write** function is gated by the completion of the previous **oclDct::read** function, resulting in the sequential behavior observed in the **Application Timeline**
1. Use the **Outline** viewer to locate the definition of the **NUM\_SCHED** macro in the **idct.cpp** file
    - This macro defines the depth of the event queue
    - The value of 1 explains the observed behavior: new tasks (write, run, read) are only enqueued when the previous has completed effectively synchronizing each loop iteration
    - By increasing the value of the **NUM\_SCHED** macro, we increase the depth of the event queue and enable more blocks to be enqueued for processing, which may result in the write, run and read tasks to overlap and allow the kernel to execute continuously or at least more frequently
    - This technique is called software pipelining
1. Modify line 213 to increase the value of **NUM\_SCHED** to 6 as follows  
**#define NUM\_SCHED 6**
1. Save the file
### Run the Hardware Emulation.
1. Change the run configuration by unchecking the **Use waveform for kernel debugging** option, click **Apply**, and then click **Close**
1. Run the application by clicking the Run button (![alt tag](./images/Fig-run.png))  
    - Since only the idct.cpp file was changed, the incremental makefile rebuilds only the host code before running emulation
    - This results in a much faster iteration loop since it is usually the compilation of the kernel to hardware which takes the most time
1. In the **Assistant** tab, expand **optimization\_lab\_example > Emulation-HW > optimization\_lab\_example-Default**
1. Double-click the **Application Timeline** report  

Observe how **software pipelining** enables overlapping of data transfers and kernel execution.
    <p align="center">
    <img src ="./images/optimization_lab/FigOptimizationLab-22.png"/>
    <img src ="./images/optimization_lab/FigOptimizationLab-22-1.png"/>
    </p>
    <p align = "center">
    <i>Application Timeline after the host code optimization</i>
    </p>  

Note: system tasks might slow down communication between the application and the hardware simulation, impacting on the measured performance results. The effect of software pipelining is considerably higher when running on the actual hardware

###  Run the Application on F1
**Since the System build and AFI availability takes considerable amount of time, a precompiled version is provided. Use the precompiled solution directory to verify the functionality**  

1. Open a new terminal window, source sdaccel environment settings, and change to the solution directory by executing the following commands  
   ```
     cd ~/aws-fpga		  
     source sdaccel_setup.sh		  
     source $XILINX_SDX/settings64.sh	  
     cd /home/centos/sources/optimization_lab_solution
   ```  
1. Run the following commands to load the AFI and execute the application to verify the functionality
   ```
      sudo sh
      source /opt/xilinx/xrt/setup.sh
      ./optimization_lab_example.exe xclbin/binary_container_1.awsxclbin
   ```
1. The FPGA bitstream will be downloaded and the host application will be executed showing output something like:
    <p align="center">
    <img src ="./images/optimization_lab/FigOptimizationLab-23.png"/>
    </p>
    <p align = "center">
    <i>Execution output</i>
    </p>  
1. Enter **exit** in the terminal window to exit out of the _sudo shell_
1. Close the SDx by selecting **File &gt; Exit**

## Conclusion

In this lab, you used SDAccel IDE to create a project and added a kernel function. After identifying the kernel you performed software and hardware emulations. You analyzed various generated reports and then you optimized kernel code using DATAFLOW and host code by increasing the number of read, write, and run tasks to improve throughput and data transfer rates. You then validated the functionality on F1.

---------------------------------------

<p align="center"><b>
Start the next lab: <a href="rtl_kernel_wizard_lab.md">5. RTL-Kernel Wizard Lab</a>
</b></p>

---------------------------------------


## Appendix Build Full Hardware 
**Set the build configuration to System and build the system (Note that since the building of the project takes over two hours skip this step in the workshop environment).**  

1. Either select **Project &gt; Build Configurations &gt; Set Active &gt; System** or click on the drop-down button of _Active build configuration_ and select **System**  
1. Set the XOCC Kernel Linker flag as done before
1. Either select **Project &gt; Build Project** or click on the (![alt tag](./images/Fig-build.png)) button    
This will build the project under the **System** directory. The built project will include **optimization\_lab\_example.exe** file along with **binary\_container\_1.xclbin** file. This step takes about two hours  

**Once the full system is built, you can create an AFI by following the steps listed <a href="Creating_AFI.md">here</a>**
