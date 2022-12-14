Vector Addition
===============

This is a simple example of vector addition.The purpose of this code is to introduce the user to application development in the Vitis tools.

.. raw:: html

 <details>

.. raw:: html

 <summary> 

 <b>EXCLUDED PLATFORMS:</b>

.. raw:: html

 </summary>
|
..

 - All NoDMA Platforms, i.e u50 nodma etc

.. raw:: html

 </details>

.. raw:: html

DESIGN FILES
------------

Application code is located in the src directory. Accelerator binary files will be compiled to the xclbin directory. The xclbin directory is required by the Makefile and its contents will be filled during compilation. A listing of all the files in this example is shown below

::

   src/vadd1_kernel.cpp
   src/vadd1_wrapper.cpp
   
COMMAND LINE ARGUMENTS
----------------------
Configure the enviroment (use your Vitis installation path and version for VITIS_DIR and VITIS_VERSION)
::
   export VITIS_DIR=/tools/Xilinx/Vitis
   export VITIS_VERSION=2020.2
   export PLATFORM=xilinx_u280_xdma_201920_3

   source $VITIS_DIR/VITIS_VERSION/settings64.sh
   source /opt/xilinx/xrt/setup.sh


Once the environment has been configured, the application can be compiled by
::
   make all TARGET=<sw_emu/hw_emu>
   
Once the compilation has been completed, the application can be executed by
::
   XCL_EMULATION_MODE=hw_emu ./vadd1_wrapper ./build_dir.hw_emu.xilinx_u280_xdma_201920_3/vadd1_kernel.xclbin

DETAILS
-------

This is a simple example of vector addition. The kernel uses HLS Dataflow which allows the user to schedule multiple task together to achieve higher throughput.

Vitis kernel can have one s_axilite interface which will be used by host application to configure the kernel. All the global memory access arguments are associated to m_axi(AXI Master Interface) as below:

.. code:: cpp	

   #pragma HLS INTERFACE m_axi port = in1 bundle = gmem0
   #pragma HLS INTERFACE m_axi port = in2 bundle = gmem1
   #pragma HLS INTERFACE m_axi port = out bundle = gmem0

Multiple interfaces can be created based on the requirements. For example when multiple memory accessing arguments need access to global memory simultaneously, user can create multiple master interfaces and can connect to different arguments.

Usually data stored in the array is consumed or produced in a sequential manner, a more efficient communication mechanism is to use streaming data as specified by the STREAM pragma, where FIFOs are used instead of RAMs.

Vector addition in kernel is divided into 4 sub-tasks(read input 1, read input 2 , compute_add and write) which are then performed concurrently using ``Dataflow``.

.. code:: cpp

   #pragma HLS dataflow
       load_input(in1, in1_stream, size);
       load_input(in2, in2_stream, size);
       compute_add(in1_stream, in2_stream, out_stream, size);
       store_result(out, out_stream, size);

To visit github.io of this repository, `click here <http://xilinx.github.io/Vitis_Accel_Examples>`__.
