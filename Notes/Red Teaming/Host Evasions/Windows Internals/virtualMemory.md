# Virtual Memory in Windows Internals

Virtual memory is a critical component of how Windows internals work and interact with each other. It allows internal components to interact with memory as if it were physical memory, without the risk of collisions between applications. The concept of modes and collisions is explained further in Task 8.

## Private Virtual Address Space

Virtual memory provides each process with a private virtual address space. A memory manager translates virtual addresses to physical addresses. By using a private virtual address space and avoiding direct writes to physical memory, processes reduce the risk of causing damage.

## Memory Management and Paging

The memory manager uses pages or transfers to handle memory. Applications may use more virtual memory than the allocated physical memory. To address this, the memory manager transfers or pages virtual memory to the disk. This concept can be visualized in the diagram below.

## Address Space Allocation

### 32-bit Systems

- **Theoretical Maximum Virtual Address Space**: 4 GB  
- **Address Space Split**:  
    - Lower half (0x00000000 - 0x7FFFFFFF): Allocated to processes.  
    - Upper half (0x80000000 - 0xFFFFFFFF): Allocated to OS memory utilization.  

Administrators can modify this allocation layout for applications requiring a larger address space using settings like `increaseUserVA` or the Address Windowing Extensions (AWE).

### 64-bit Systems

- **Theoretical Maximum Virtual Address Space**: 256 TB  
- The exact address layout ratio from the 32-bit system is applied to the 64-bit system. Most issues requiring settings or AWE are resolved with this increased theoretical maximum.

## Importance of Understanding Virtual Memory

Although this concept does not directly translate to Windows internals or other concepts, understanding it is crucial. When understood correctly, it can be leveraged to aid in abusing Windows internals.