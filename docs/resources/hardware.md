# PCF hardware

## IBM Blue Gene/Q - Avoca

* Peak performance of 838.86 teraFLOPS.
* 65,536 PowerPC based 1.6GHz cores.
* A total of 64TB RAM.
* Interconnect between compute nodes forms a five-dimensional torus providing excellent nearest neighbour and bisection bandwidth.
* Suitable for large-scale parallel processing.
* Compute nodes run a custom lightweight operating system called Compute Node Kernel (CNK) that is similar to Linux and mostly POSIX compliant.
* The head node runs runs the RHEL 6 operating system, a variety of Linux.

## IBM iDataplex x86 system - Merri

* Peak performance of 7.3 teraFLOPS.
* 688 Intel Nehalem compute cores running at 2.66GHz.
* 36 nodes with 96GB RAM and 8 cores per node.
* 44 nodes with 48GB RAM and 8 cores per node.
* 3 nodes with 1024GB RAM and 16 cores per node.
* Connected to a high speed, low latency QDR Voltair InfiniBand switch for inter-process communications.
* The system runs the RHEL 6 operating system, a variety of Linux.

## IBM iDataplex x86 system - Barcoo

* Peak performance - compute nodes currently performing at 20 teraFLOPS - with Xeon Phi cards running nominally at 1 teraFLOP each
* 1120 Intel Sandybridge compute cores running at 2.7GHz.
* 67 nodes with 256GB RAM and 16 cores per node.
* 3 nodes with 512GB RAM and 16 cores per node.
* 20 Xeon Phi 5110P cards installed across 10 nodes.
* Connected to a high speed, low latency Mellanox FDR14 InfiniBand switch for inter-process communications.
* The system runs the RHEL 6 operating system, a variety of Linux.

Computing resources on these Supercomputers may be applied for under the VLSCI Resource Allocation Scheme.

## Storage infrastructure:

* 700TB GPFS Parallel Data Store (shared by Barcoo, Merri and Avoca)
* 1PB HSM tape system, made available through GPFS (shared by Barcoo, Merri and Avoca)
