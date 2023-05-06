Download Link: https://assignmentchef.com/product/solved-ecs150-project3
<br>
You will be continuing the development of your virtual machine. You will be adding the ability to create, allocate, deallocate, query, and delete memory pools. Except were specified in this description the API will remain as stated in the Project 2 description. The memory pools allow for dynamic memory allocation from specified pools of memory,

there is a main system memory pool (VM_MEMORY_POOL_ID_SYSTEM) that is

dynamically allocated by the virtual machine at the beginning of execution. The stack space for each of the threads must be allocated from this memory pool. Space is allocated from the memory pools using a first fit algorithm.

The communication between the virtual machine and machine has changed. The MachineFileRead and MachineFileWrite functions require that shared memory locations be used to transfer data to/from the machine. In addition the maximum amount of data transferred must be limited to 512 bytes.




A working example of the vm and apps can be found in /home/cjnitta/ecs150. The vm syntax is vm [options] appname [appargs]. The possible options for vm are -t, -h, -m, and -s; -t specifies the tick time in millisecond, -h specifies the size of the heap this is the size of the VM_MEMORY_POOL_ID_SYSTEM memory pool, -m specifies the machine

timeout/responsiveness in milliseconds, and -s specifies the size of the shared memory used between the virtual machine and machine. By default the times are set to 10ms, for debugging purposes you can increase these values to slow the running of the vm. The size of the heap by default is 16MiB, and the shared memory is 16KiB. When specifying the application name the ./ should be prepended otherwise vm may fail to load the shared object file.




The function specifications for both the virtual machine and machine are provided in the subsequent pages.




You should avoid using existing source code as a primer that is currently available on the Internet. You <strong>must</strong> specify in your readme file any sources of code that you or your partner have viewed to help you complete this project. All class projects will be submitted to MOSS to determine if pairs of students have excessively collaborated with other pairs. Excessive collaboration, or failure to list external code sources will result in the matter being transferred to Student Judicial Affairs.

<strong><em>Name </em></strong>

VMStart – Start the virtual machine.

<h1>Synopsys</h1>

<strong>#include</strong> “VirtualMachine.h”




TVMStatus VMStart(<strong>int</strong> tickms, TVMMemorySize heapsize,  <strong>int</strong> machinetickms, TVMMemorySize sharedsize, <strong>int</strong> argc,  <strong>char</strong> *argv[]);

<h1>Description</h1>

VMStart() starts the virtual machine by loading the module specified by <em>argv</em>[0]. The <em>argc</em> and <em>argv</em> are passed directly into the VMMain() function that exists in the loaded module. The time in milliseconds of the virtual machine tick is specified by the <em>tickms</em> parameter, the machine responsiveness is specified by the <em>machinetickms</em>. The heap size

of the virtual machine is specified by <em>heapsize</em>. The heap is accessed by the applications through the VM_MEMORY_POOL_ID_SYSTEM memory pool. The size of the shared memory space between the virtual machine and the machine is specified by the <em>sharedsize</em>.

<h1>Return Value</h1>

Upon successful loading and running of the VMMain() function, VMStart() will return VM_STATUS_SUCCESS after VMMain() returns. If the module fails to load, or the module does not contain a VMMain() function, VM_STATUS_FAILURE is returned.




<strong><em>Name </em></strong>

VMMemoryPoolCreate – Creates a memory pool from an array of memory.

<h1>Synopsys</h1>

<strong>#include</strong> “VirtualMachine.h”




TVMStatus VMMemoryPoolCreate(<strong>void</strong> *base, TVMMemorySize size,

TVMMemoryPoolIDRef memory);

<h1>Description</h1>

VMMemoryPoolCreate() creates a memory pool from an array of memory. The base and size of the memory array are specified by <em>base</em> and <em>size</em> parameters respectively. The memory pool identifier is put into the location specified by the <em>memory</em> parameter.

<h1>Return Value</h1>

Upon successful creation of the memory pool, VMMemoryPoolCreate() will return

VM_STATUS_SUCCESS. If the <em>base</em> or <em>memory</em> are NULL, or <em>size</em> is zero VM_STATUS_ERROR_INVALID_PARAMETER is returned.

<strong><em>Name </em></strong>

VMMemoryPoolDelete – Deletes a memory pool from the virtual machine.

<h1>Synopsys</h1>

<strong>#include</strong> “VirtualMachine.h”




TVMStatus VMMemoryPoolDelete(TVMMemoryPoolID memory);

<h1>Description</h1>

VMMemoryPoolDelete() deletes a memory pool that has no memory allocated from the pool. The memory pool to delete is specified by the <em>memory</em> parameter.

<h1>Return Value</h1>

Upon successful deletion of the memory pool, VMMemoryPoolDelete() will return VM_STATUS_SUCCESS. If the memory pool specified by <em>memory</em> is not a valid

memory pool, VM_STATUS_ERROR_INVALID_PARAMETER is returned. If any memory has been allocated from the pool and not deallocated, then VM_STATUS_ERROR_INVALID_STATE is returned.







<strong><em>Name </em></strong>

VMMemoryPoolQuery – Queries the available space in the memory pool.

<h1>Synopsys</h1>

<strong>#include</strong> “VirtualMachine.h”




TVMStatus VMMemoryPoolQuery(TVMMemoryPoolID memory,

TVMMemorySizeRef byesleft);

<h1>Description</h1>

VMMemoryPoolQuery() queries a memory pool for the available memory left in the pool. The memory pool to query is specified by the <em>memory</em> parameter. The space left unallocated in the memory pool is placed in the location specified by <em>bytesleft</em>.

<h1>Return Value</h1>

Upon successful querying of the memory pool, VMMemoryPoolQuery() will return VM_STATUS_SUCCESS. If the memory pool specified by <em>memory</em> is not a valid memory pool or <em>bytesleft</em> is NULL, VM_STATUS_ERROR_INVALID_PARAMETER is returned.




<strong><em>Name </em></strong>

VMMemoryPoolAllocate – Allocates memory from the memory pool.

<h1>Synopsys</h1>

<strong>#include</strong> “VirtualMachine.h”




TVMStatus VMMemoryPoolAllocate(TVMMemoryPoolID memory,

TVMMemorySize size, void **pointer);

<h1>Description</h1>

VMMemoryPoolAllocate() allocates memory from the memory pool. The memory pool to allocate from is specified by the <em>memory</em> parameter. The size of the allocation is specified by <em>size</em> and the base of the allocated array is put in the location specified by <em>pointer</em>.

<h1>Return Value</h1>

Upon successful allocation from the memory pool, VMMemoryPoolAllocate() will return VM_STATUS_SUCCESS. If the memory pool specified by <em>memory</em> is not a valid memory pool, <em>size</em> is zero, or <em>pointer</em> is NULL,

VM_STATUS_ERROR_INVALID_PARAMETER is returned. If the memory pool does

not have sufficient memory to allocate the array of <em>size</em> bytes,

VM_STATUS_ERROR_INSUFFICIENT_RESOURCES is returned.




<strong><em>Name </em></strong>

VMMemoryPoolDeallocate – Deallocates memory to the memory pool.

<h1>Synopsys</h1>

<strong>#include</strong> “VirtualMachine.h”




TVMStatus VMMemoryPoolDeallocate(TVMMemoryPoolID memory,  void *pointer);

<h1>Description</h1>

VMMemoryPoolDeallocate() deallocates memory to the memory pool. The memory pool to deallocate to is specified by the <em>memory</em> parameter. The base of the previously allocated array is specified by <em>pointer</em>.

<h1>Return Value</h1>

Upon successful deallocation from the memory pool, VMMemoryPoolDeallocate() will return VM_STATUS_SUCCESS. If the memory pool specified by <em>memory</em> is not a valid

memory pool, or <em>pointer</em> is NULL, VM_STATUS_ERROR_INVALID_PARAMETER

is returned. If <em>pointer</em> does not specify a memory location that was previously allocated from the memory pool, VM_STATUS_ERROR_INVALID_PARAMETER is returned.







<strong><em>Name </em></strong>

MachineInitialize – Initializes the machine abstraction layer.

<h1>Synopsys</h1>

<strong>#include</strong> “Machine.h”

<strong>void</strong> *MachineInitialize(<strong>int</strong> timeout, size_t sharesize);

<h1>Description</h1>

MachineInitialize() initializes the machine abstraction layer. The <em>timeout</em> parameter specifies the number of milliseconds the machine will sleep between checking for requests. Reducing this number will increase its responsiveness. The <em>sharesize</em> parameter specifies the size of the shared memory location to be used by the machine. The size of the shared memory will be set to an integral number of pages (4096 bytes) that covers the size of <em>sharesize</em>.

<h1>Return Value</h1>

Upon successful initialization MachineInitialize returns the base address of the shared memory. NULL is returned if the machine has already been initialized. If the memory queues or shared memory fail to be allocated the program exits.




<strong><em>Name </em></strong>

MachineFileRead – Reads from a file in the machine abstraction.

<h1>Synopsys</h1>

<strong>#include</strong> “Machine.h”

<strong>typedef</strong> <strong>void</strong> (*TMachineFileCallback)(<strong>void</strong> *calldata, <strong>int</strong> result);




<strong>void</strong> MachineFileRead(<strong>int</strong> fd, <strong>void</strong> *data, <strong>int</strong> length, TMachineFileCallback callback, <strong>void</strong> *calldata);

<h1>Description</h1>

MachineFileRead() attempts to read the number of bytes specified in by <em>length</em> into the location specified by <em>data</em> from the file specified by <em>fd</em>. If the <em>data</em> value is not a location in the shared memory, MachineFileRead will fail; in addition if length is greater than 512 bytes MachineFileRead will also fail. The <em>fd</em> should have been obtained by a previous call to MachineFileOpen(). The actual number of bytes transferred will be returned in the <em>result</em> parameter when the <em>callback</em> function is called. Upon failure the <em>result</em> will be less than zero. The <em>calldata</em> parameter will also be passed into the <em>callback</em> function upon completion of the read file request. MachineFileRead () should return immediately, but will call the <em>callback</em> function asynchronously when completed.

<strong><em>Return Value </em></strong>

N/A




<strong><em>Name </em></strong>

MachineFileWrite – Writes to a file in the machine abstraction.

<h1>Synopsys</h1>

<strong>#include</strong> “Machine.h”

<strong>typedef</strong> <strong>void</strong> (*TMachineFileCallback)(<strong>void</strong> *calldata, <strong>int</strong> result);




<strong>void</strong> MachineFileWrite(<strong>int</strong> fd, <strong>void</strong> *data, <strong>int</strong> length, TMachineFileCallback callback, <strong>void</strong> *calldata);

<h1>Description</h1>

MachineFileWrite() attempts to write the number of bytes specified in by <em>length</em> into the location specified by <em>data</em> to the file specified by <em>fd</em>. If the <em>data</em> value is not a location in the shared memory, MachineFileWrite will fail; in addition if length is greater than 512 bytes MachineFileWrite will also fail. The <em>fd</em> should have been obtained by a previous call to MachineFileOpen(). The actual number of bytes transferred will be returned in the <em>result</em> parameter when the <em>callback</em> function is called. Upon failure the <em>result</em> will be less than zero. The <em>calldata</em> parameter will also be passed into the <em>callback</em> function upon completion of the write file request. MachineFileWrite() should return immediately, but will call the <em>callback</em> function asynchronously when completed.

<h1>Return Value</h1>

N/A