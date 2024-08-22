.. meta::
  :description: This chapter describes the HIP runtime API and the compilation workflow of the HIP compilers.
  :keywords: AMD, ROCm, HIP, CUDA, HIP runtime API

.. _hip_runtime_api_understand:

*******************************************************************************
HIP runtime API
*******************************************************************************

The HIP runtime API is the programming interface, which provides C and C++
functions for event, stream,  device and memory managements, etc. The HIP
runtime on AMD platform uses the :doc:`Common Language Runtime (CLR) <hip:understand/amd_clr>`,
while on NVIDIA platform HIP runtime is only a thin layer over the CUDA runtime
or Driver API.

- **CLR** contains source codes for AMD's compute languages runtimes: ``HIP``
  and ``OpenCL™``. CLR includes the implementation of ``HIP`` language on the AMD
  platform `hipamd <https://github.com/ROCm/clr/tree/develop/hipamd>`_ and the
  Radeon Open Compute Common Language Runtime (rocclr). rocclr is a virtual device
  interface, that HIP runtime interact with different backends such as ROCr on
  Linux or PAL on Windows. (CLR also include the implementation of `OpenCL <https://github.com/ROCm/clr/tree/develop/opencl>`_,
  while it's interact with ROCr and PAL)
- **CUDA runtime** is built over the CUDA driver API (lower-level C API). For
  further information about the CUDA driver and runtime API, check the :doc:`hip:how-to/hip_porting_driver_api`.
  On non-AMD platform, HIP runtime determines, if CUDA is available and can be
  used. If available, HIP_PLATFORM is set to ``nvidia`` and underneath CUDA path
  is used.

The different runtimes interactions are represented on the following figure.

.. figure:: ../data/understand/programming_interface/runtimes.svg

.. note::

  The CUDA specific headers can be found in the `hipother repository <https://github.com/ROCm/hipother>`_.

HIP compilers
=============

The HIP runtime API and HIP C++ extensions are available with HIP compilers. On
AMD platform ROCm currently provides two compiler interfaces: ``hipcc`` and
``amdclang++``. The ``hipcc`` command-line interface aims to provide a more
familiar user interface to users who are experienced in CUDA but relatively new
to the ROCm/HIP development environment. On the other hand, ``amdclang++``
provides a user interface identical to the clang++ compiler. (For further
details, check :doc:`llvm <llvm-project:index>`). On NVIDIA platform ``hipcc``
invoke the locally installed ``NVCC`` compiler, while on AMD platform it's
invoke ``amdclang++``.

.. Need to update the link later.
For AMD compiler options, check the :doc:`GPU compiler option page <llvm-project:reference/rocmcc>`.

HIP compilation workflow
------------------------

The source code compiled with HIP compilers are separated to device code and
host. The HIP compilers:

* Compiling the device code into an assembly.
* Modify the host code by replacing the ``<<<...>>>`` syntax introduced in
  kernels by the necessary CUDA runtime function calls to load and launch each
  compiled kernel from the ``PTX`` code and/or ``cubin`` object.

``NVCC`` and ``amdclang++`` target different architectures and use different
code object formats: ``NVCC`` is ``cubin`` or ``ptx`` files, while the
``amdclang++`` path is the ``hsaco`` format.

For example of compiling from command line, check the :ref:`SAXPY tutorial compiling <compiling_on_the_command_line>` .

.. _driver_api_understand:

Driver API 
===========

The driver API offers developers low-level control over GPU operations, enabling
them to manage GPU resources, load and launch kernels, and handle memory
explicitly. In HIP the Driver API is part of the runtime API, while the CUDA's
driver API is separate from CUDA runtime API.

One significant advantage of the driver API is its ability to dynamically load
and manage code objects, which is particularly useful for applications that need
to generate or modify kernels at runtime. This flexibility allows for more
sophisticated and adaptable GPU programming.

Unlike the runtime API, the driver API does not automatically handle tasks such
as context creation and kernel loading. While the runtime API is more convenient
and easier to use for most applications, the driver API provides greater control
and can be more efficient for complex or performance-critical applications.

Using the driver API can result in longer development times due to the need for
more detailed code and explicit management. However, the actual runtime
performance can be similar to or even better than the runtime API, depending on
how well the application is optimized.

For further details, check :ref:`porting_driver_api`, and :ref:`driver_api_reference`.

Execution Control
=================

Device memory
=============

Device memory exists on the device (e.g. GPU) of the machine in video random
access memory (VRAM). Recent architectures use graphics double data rate (GDDR)
synchronous dynamic random-access memory (SDRAM)such as GDDR6, or high-bandwidth
memory (HBM) such as HBM2e.

Global Memory 
--------------

Read-write storage visible to all threads in a given grid. There are specialized
versions of global memory with different usage semantics which are typically
backed by the same hardware storing global.

Shared Memory
-------------

Read-write storage visible to all the threads in a given block.

Local or per-thread memory
--------------------------

Read-write storage only visible to the threads defining the given variables,
also called per-thread memory. The size of a block for a given kernel, and thereby
the number of concurrent warps, are limited by local memory usage.
This relates to an important aspect: occupancy. This is the default memory
namespace.

Constant Memory
---------------

Read-only storage visible to all threads in a given grid. It is a limited
segment of global with queryable size.

Texture Memory
--------------------------

Read-only storage visible to all threads in a given grid and accessible
through additional APIs.

Surface Memory
--------------------------

A read-write version of texture memory.

Memory Management
=================

Managed Memory
--------------

In conventional architectures, CPUs and GPUs have dedicated memory like Random
Access Memory (RAM) and Video Random Access Memory (VRAM). This architectural
design, while effective, can be limiting in terms of memory capacity and
bandwidth, as continuous memory copying is required to allow the processors to
access the appropriate data. New architectural features like Heterogeneous
System Architectures (HSA) and Unified Memory (UM) help avoid these limitations
and promise increased efficiency and innovation.

Stream Ordered Memory Allocator
--------------------------------

Stream Ordered Memory Allocator (SOMA) provides an asynchronous memory
allocation mechanism with stream-ordering semantics. You can use SOMA to
allocate and free memory in stream order, which ensures that all asynchronous
accesses occur between the stream executions of allocation and deallocation.
Compliance with stream order prevents use-before-allocation or use-after-free
errors, which helps to avoid an undefined behavior.


Virtual Memory Management
--------------------------------

Memory management is important when creating high-performance applications in
the HIP ecosystem. Both allocating and copying memory can result in bottlenecks,
which can significantly impact performance.

Global memory allocation in HIP uses the C language style allocation function.
This works fine for simple cases but can cause problems if your memory needs
change. If you need to increase the size of your memory, you must allocate a
second larger buffer and copy the data to it before you can free the original
buffer. This increases overall memory usage and causes unnecessary ``memcpy``
calls. Another solution is to allocate a larger buffer than you initially need.
However, this isn't an efficient way to handle resources and doesn't solve the
issue of reallocation when the extra buffer runs out.

Virtual memory management solves these memory management problems. It helps to
reduce memory usage and unnecessary ``memcpy`` calls.

Texture Management
----------------------

* Global enum and defines
* Initialization and Version
* 
* 
* Error Handling

Stream Management
=================

Stream management refers to the mechanisms that allow developers to control the
order and concurrency of kernel executions and memory transfers on the GPU.
Stream is linear sequence of execution which belonging to a specific GPU. 
Different streams can execute operations concurrently on the same GPU, which can
lead to better utilization of the device.

Stream management allows developers to optimize GPU workloads by enabling
concurrent execution of tasks, overlapping computation with memory transfers,
and controlling the order of operations. The priority can be also set, which 
gives extra flexibility in the developers hand.


Callback Activity APIs
=================

Graph Management
=================

OpenGL Interop
================

Surface Object
=================

For further details, check `HIP Runtime API Reference <doxygen/html/index.html>`_.