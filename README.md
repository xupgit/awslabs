# awslabs_2018_2
<table style="width:100%">
  <tr>
    <th width="100%" colspan="5"><h2>XUP AWS F1 Labs</h2></th>
  </tr>
  <tr>
    <td width="16%" align="center"><a href="Connecting_to_AWS_lab.md">1. Connecting to your F1 instance</a></td> 
    <td width="16%" align="center"><a href="Makefile_Flow_lab.md">2. Makefile Flow Lab</a></td>
    <td width="16%" align="center"><a href="GUI_Flow_lab.md">3. GUI Flow Lab</a></td>
    <td width="16%" align="center"><a href="Optimization_lab.md">4. Optimization Lab</a></td>
    <td width="16%" align="center"><a href="rtl_kernel_wizard_lab.md">5. RTL-Kernel Wizard Lab</a></td>
    <td width="16%" align="center"><a href="debug_lab.md">6. Debug Lab</a></td>
  </tr>
</table>

---------------------------------------
### Introduction

Welcome to the XUP AWS F1 Labs. These labs will provide you hands-on experience with AWS F1 when connected to AWS F1 instance using workshop accounts.  You will learn how to develop accelerated applications using the AWS F1 OpenCL flow and the Xilinx SDAccel development environment.

#### Overview of the AWS F1 platform and SDAccel flow

The architecture of the AWS F1 platform and the SDAccel development flow are pictured below:

![alt tag](./images/f1_platform.png)

1. Amazon EC2 F1 is a compute instance combining x86 CPUs with Xilinx FPGAs. The FPGAs are programmed with custom hardware accelerators which can accelerate complex workloads up to 30x when compared with servers that use CPUs alone. 
2. An F1 application consists of an x86 executable for the host application and an FPGA binary (also referred to as Amazon FPGA Image or AFI) for the custom hardware accelerators. Communication between the host application and the accelerators are automatically managed by the OpenCL runtime.
3. SDAccel is the development environment used to create F1 applications. It comes with a fully fledged IDE, x86 and FPGA compilers, profiling and debugging tools.
4. The host application is written in C or C++ and uses the OpenCL API to interact with the accelerated functions. The accelerated functions (also referred to as kernels) can be written in C, C++, OpenCL or even RTL.


#### Overview of the XUP AWS F1 Labs 

There are five labs. It is recommended to complete each lab before proceeding to the next.

1. **Connecting to Your F1 Instance** \
You will start a pre-configured EC2 F1 instance and connect to it using a remote desktop client. 
1. **Makefile Flow Lab** \
This lab guides you through the steps involved in using a Makefile flow to build and perform CPU and hardware emulation to verify the functionality. You will then use an AWS F1 instance to validate the design. 
1. **GUI Flow Lab** \
This lab guides you through the steps involved in using a GUI flow to create an SDAccel project. After creating a project you will run CPU and hardware emulation to verify the functionality. You will then use an AWS F1 instance to validate the design on F1.
1. **Optimization Lab** \
This lab guides you through the steps involved in creating a project and adding a kernel function. After creating a project you will run CPU and hardware emulation to verify the functionality, analyze various generated reports and then apply techniques both on host and kernel side to improve throughput and data transfer rate.
1. **RTL-Kernel Wizard Lab** \
This lab guides you through the steps involved in using a RTL Kernel wizard to wrap a user RTL-based IP so the generated IP can be used in SDAccel project.

Since building FPGA binaries is not instantaneous, all the labs will use precompiled FPGA binaries.

---------------------------------------

<p align="center"><b>
Start the first lab: <a href="Connecting_to_AWS_lab.md">1. Connecting to your F1 instance</a>
</b></p>
