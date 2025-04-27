# Memory_Management


What are the components in the JVM?
==================================
 
Components in the JVM memory model :  Java 7

	1. Heap
	2. Stack
	3. Method Area
	4. Native memory
	5. PC register

Where are the objects stored ? Heap / Stack.

Garbase Collecitons :

    Steps : 
    		- Marking
    		- Normal deletion
    		- deletion + compacting


Types :

    1. Serial GC
    2. Parallel GC
    3. Parallel Old GC
    4. Concurrent Mark Sweep
    5. G1 GC 


Memory Management:
-----------------

	2 Types of Memory:

		1. Stack
		
		   * All are local variables and method arguments and refernce of heap objects are stored.
		
		2. Heap
		   
		   * App primitive object references are stored in heap
		   * static objects are also stored in heap.
		   

	Both memory created by JVM and was stored in RAM.
	
	Stack Memory :
	------------
	
	
	- Store temporary variables and separate memory block for methods.
	- Store primitive data types.
	- Store reference of heap objects.
	   Strong reference
	   Weak reference
	     Soft reference
	- Each thread has its own stack memory.
	- Variables within a SCOPE is only visible and as soon as any variable goes out of the SCOPE, it get deleted from the Stack (in LIFO order).
	- When Stack memory goes full, its throws "java.lang.StackOverflowError".
	
	
	Heap Memory :
	-----------
	
	- Store Objects.
	- There is no order of allocating the memory.
	- Garbage Collector is used to delete the unreferenced objects from the heap.
	   * Mark and Sweep Algorithm
	   * Types of GC:
	      - Single GC  -> only one thread will work and freedup the space.
		  - Parallel GC
		  - CMS (Concurrent Mark Sweep)
		  - G1
	- Heap memory is shared with all the threads.
	- Heap also contains the String pool.
	- When Heap Memory goes full, its throws "java.lang.OutOfMemoryError"
	- Heap memory is further divided into:
	   * Young Generation (minor GG happens here)
	     Eden
		 Survior
	   * Old Generation (Major GC happens here)
	   * Permanent Generation / Meta space
	 
	
	Reference :
	----------
	
	- Strong reference : it will (reference will noe delete) not delete, it will survive. 
	  
	  Example :  Person person = new Person();
				 
				 person is the strong reference.
				 
	- Weak reference :
	
	  WeakReference<Person>  weakReference = new WeakReference<Person> (new Person()):
	  
	  Just informing to GC , this is weak reference, as soon as start/run GC . This weakReference automatically will delete.
	  
	- Soft reference :
	
	  When ever GC is urgent or need and it is required.GC will tell i have sufficient space..it it is required will delete it.
	  

Garbage Collection :   
------------------
      
	* Java provides automatic memory management through program called Garbage Collector.
	
	* Live Object ( it is reachable )
	* Dead Object ( Unreachable )
	* We can not force GC.(System.gc())

    * Key feature of the java platform

    * Automatic memory management

        - Fast allocation
        - Efficient	reclamation
		
	Choosing your CG :
	
	   - Main focus for the application
	   - Three different aspects
	        * Throughput
			* Latency
			* Footprint
	   - Hard to optimize
	        * trade-offs
			
			
	Open JDK collectors :
	-------------------
	
	Collector Name				Focus area
	---------------				----------
	
	Serial						Memory foot print
	
	Parallel					Throughput
	
	G1							Balanced performance
	
	ZGC							Low latency
	
	
	Serial :
	
	* Low memory overhead
	* Generation collection 
	* Single Threaded STW collections
	* Suitable for small containers
																Heap Layout
																
													Young Generation		Old Generation
	
	
	
	Parallel :
	
	* Throughput oriented
	* Generation collection 
	* Multi Threaded STW collections
	* Default collector up until jdk 8
	
																Heap Layout
																
													Young Generation		Old Generation
	
	G1 :
	
	* Balance between throughput and latency
	* Region bases
	* Concurrent marking
	* Mixed collections
	* Default collector since Jdk 9
	
	
																Heap Layout
																
													Young Generation		Old Generation
													
    ZGC :
	
	* Low latency and scalable
	* Region based
	* Fully concurrent collections
	* Efficient load barriers
	* Generational collections since jdk 21
	
																Heap Layout
																
													Young Generation		Old Generation
													
	Summary :
	--------
	
	Collector Name			Summary
	--------------			-------
	
	Serial					Low overhead, suitable for small containers
    Parallel                Great throughput, suitable for workloads where worse case latencies are acceptable
    G1                      Good throughput ; Controllable latency , suitable for long lived services where heap size are below 16 GB
    ZGC 				    Good throughput	, ultra-low latency, suitable for long lived services where heap sizez are above 4 GB.
	
	
Understanding Heap Memory and GC :
--------------------------------

	Does java process memory utilization go beyond  -Xmx?
	
	
	JavaProcessMemory
	-----------------
	
	Heap Memory												Native Memory
	
	Young      Old								Metaspace          |     Threads     |    Code Cache  | Direct Buffer Region  |   GC  | JNI | MISC 
	
	-Xmx								-XX: MaxMetaspace Size						
		

OutofMemoryError:
----------------

	* How to know the type of OutOfMemoryError?
	-------------------------------------------
	
	Ans :
	
	java.lang.Out.OfMemoryError: <type>       --> what type is , here it is printing in the type.
	
	Example : 
	
	1. java.lang.Out.OfMemoryError: Java heap space
	-----------------------------------------------

	How to study GC behaviour ?

		Enable GC logs (always) - Almost zero overhead.

		Till Java 8 :

		-XX:+PrintGCDetails -XX:+PrintGCDateStamps -Xloggc:<file-path>

		From Java 9:

		-Xlog:gc*:file=<file-path>
		
	Tool : 
	
	GCeasy tool will do the analyze.
	
	Causes :

	1. Increase in Traffice volume

	2, Memory leak due to buggy code


	Soultions :

	1. Fix memory leak in the code
	2. Increase heap size (-Xmx)
	
	
	Artifacts :
	
	1. Garbage collection logs
	2. Heap Dump
	
	
	2. OutOfMemoryError:GC overhead limit exceeded:
	-----------------------------------------------
	
	Java process is spending more than 98% of its time doing garbage collection and recovering less than 20% of the heap and has been doing so far the last 5 consecutive garbase collections.
	
	
	Tools :
	
	1. GCeasy ( GC log analysis)
	2. HeapPro
	3. Eclipse MAT
	4. JVisualVM
	
	3. Requested array size exceeds VM limit:
	-----------------------------------------
	
	Causes :
	
	1. Allocated array larger than heap size.
	
	
	Solutions :
	
	1. Fix code which allocates large array size
	2. Increase heap size. (-Xmx)
	
	
	4. Metaspace :
	-------------
	
	Causes :
	
	1. Creating large number of Dynamic classes (Java Reflection , Groovy type of scripting languages)
	2. Loading large number of classes (3rd party libraries / frameworks)
	3. Loading large number of classloaders.
	
	Solutions :
	
	1. Fix memory leak code
	2. Increase -XX: MetespaceSize and -XX:MaxMeteaspaceSize
	
	Artifacts :
	
	1. Java 8 & below : java {app_name verbose:class 	
	2. java 9 & above L java {app_name
       Xlog:class+load=info:<filename>
	3. jcmd {pid} GC.class_historgram
	4. Heap Dump
	
	Tools :
	
	1. GCeasy ( GC log analyziz)
	2. HeapHero
	3. Eclipse MAT	
	
	
	5. Permgen space :
	------------------
	
	Happens only in java 7 and below versions.
	
	6. Unable to create new native methods :
	----------------------------------------
	
	Causes :
	
	1. Threads are leaking.
	
	Solutions :
	
	1. Fix thread leak.
	2. Increase the thread limits set as operating system (ulimit -u)
	3. Kill other processes
	4. Increase RAM capacity
	5. Reduce Java Heap size
	6. Reduce thread stack size (-Xss)
 
    Note :  can cause StackOverflowError.
	
	Artifacts :
	
	1. Thread Dump
	
	Tools :
	
	1. Text Editor
	2. FastThread
	
	7. Direct buffer memory :
	------------------------
	
	Becoming pervasive in modern spring boot frameworks.
	
	Causes :
	
	1. Increase (or Leak) in direct buffers
	
	Usuage :
	  
	  a. java.nio.package
	  b. moving from spring RestTemplate to Webclient
	  c. Image processing libraries
	  d. Networking libraries
	  e. Some JDBC Drivers
	  
	Solutions :
	
	1. Fix Memory leak code
	2. Increase -XX:MaxDirectMemorySize 
	3. Upgrade to Java 17 (There some issues in Java 11)
	
	Artifacts :
	
	1. App Log or Std Error
	2. Native memory tracking.
	
	Tools :
	
	No need any special tools.
	
	8. Kill process or sacrifice child :
	-----------------------------------
	
	9. Reasone stack_trace_with_native_method :
	------------------------------------------
	
	Cause :
	
	1. Heavy usage of native methods.
	2. Recursive natvie method calls.
	
	Solutons :
	
	1. Fix the problem in the native app.
	
	Artifacts :
	
	1. Application Log or std error
	
	Tools :
	
	1. OS native tools
	
	   a. Dtrace
	   b. pmap
	   c. pstack
	   
	
	
	
	
