.. meta::
  :description: This chapter will present the conception of driver API.
  :keywords: AMD, ROCm, HIP, CUDA, driver API

.. _driver_api:

*******************************************************************************
Driver API 
*******************************************************************************

The driver API offers developers low-level control over GPU operations, enabling them to manage GPU resources, load and launch kernels, and handle memory explicitly. This API is more flexible and powerful compared to the runtime API, but it requires a deeper understanding of the GPU architecture and more detailed management.

One significant advantage of the driver API is its ability to dynamically load and manage code objects, which is particularly useful for applications that need to generate or modify kernels at runtime. This flexibility allows for more sophisticated and adaptable GPU programming.

Memory management with the driver API involves explicit allocation, de-allocation, and data transfer operations. This level of control can lead to optimized performance for specific applications, as developers can fine-tune memory usage. However, it also demands careful handling to avoid memory leaks and ensure efficient memory utilization.

Unlike the runtime API, the driver API does not automatically handle tasks such as context creation and kernel loading. While the runtime API is more convenient and easier to use for most applications, the driver API provides greater control and can be more efficient for complex or performance-critical applications.

Using the driver API can result in longer development times due to the need for more detailed code and explicit management. However, the actual runtime performance can be similar to or even better than the runtime API, depending on how well the application is optimized.

While AMD HIP does not have a direct equivalent to CUDA's Driver API, it supports driver API functionalities, such as managing contexts, modules, memory, and driver entry point access. These features are detailed in :ref:`porting_driver_api`, and described in :ref:`driver_api_reference`.
