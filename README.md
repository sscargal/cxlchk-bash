# pmemchk

# Description 
`pmemchk` can be used to confirm a system with persistent memory is healthy and the configuration looks sane. pmemchk has two main modules:
- Collector - runs ipmctl, ndctl, and cxl commands. Also collects minimal OS information and the /var/log/messages file.
- Analyzer - Runs several tests against the collected data and reports whether the test 'Passes' or 'Failed'. If a test fails, there should be some information to explain why.

# Help

```
# ./pmemchk -h
=======================================================================
Starting PMem Checker
pmemchk Version 0.1.0
Started: Fri Jan 14 05:34:12 PM MST 2022
=======================================================================

Usage: ./pmemchk [optional args]

Checks for known issues with persistent memory configurations.
Run with root privilege.

Optional args:
   -A <Path to dataset>
      Specify the path to a dataset. Skips running the collector.

   -h,-?
      Display this help

   -i <Path to IPMCTL executable>
      Specify the path to the IPMCTL executable

   -c <Path to CXL executable>
      Specify the path to the CXL executable

   -n <Path to NDCTL executable>
      Specify the path to the NDCTL executable

   -v
      Print verbose output. Use -v, -vv, and -vvv to increase verbosity.
```

# Examples

Execute `pmemchk` which runs the Collector and Analyzer

```
$ sudo ./pmemchk
```

Execute `pmemchk` analysis only against a previously collected dataset

```
$ sudo ./pmemchk -A ./pmemchk.hostname.0113-1210
```

# Example Output
The example output below shows what to expect when running the data collector and analysis
```
$ sudo ./pmemchk
=======================================================================
Starting PMem Checker
pmemchk Version 0.1.0
Started: Fri Jan 14 05:44:47 PM MST 2022
=======================================================================
Using NDCTL command: /usr/local/bin/ndctl
NDCTL version: 72
Using IPMCTL command: /usr/local/bin/ipmctl
IPMCTL version: 02.00.00.3871
Using CXL command: /usr/local/bin/cxl
CXL version: 72
Operating System: Fedora Linux 35 (Server Edition)
Kernel Version : 5.15.10-200.fc35.x86_64
CPU(s):                          96
Model name:                      Intel(R) Xeon(R) Platinum 8260L CPU @ 2.40GHz
Socket(s):                       2
NUMA node(s):                    2
NUMA node0 CPU(s):               0-23,48-71
NUMA node1 CPU(s):               24-47,72-95
=======================================================================
Starting data collector
=======================================================================
Running IPMCTL Collector
................................
Running NDCTL Collector
........
Running CXL Collector
...
Collecting files
.
Collecting command outputs
..
=======================================================================
Data collector completed
=======================================================================
=======================================================================
Starting analysis of the data
=======================================================================
[ PASSED  ] optane_check_region_freecapacity : All Regions have the same FreeCapacity
[ PASSED  ] optane_check_dimm_lockstate : All PMem Module Security LockState is good
[ PASSED  ] optane_check_dimm_firmware_version : All PMem Modules have the same firmware.
[ PASSED  ] optane_check_dimm_health_status : All PMem Modules are Healthy
[ PASSED  ] optane_check_dimm_population : Detected 12 PMem modules. Population looks good.
[ PASSED  ] optane_check_region_capacity : All Regions have the same Capacity
[ PASSED  ] optane_check_region_health : All Regions are Healthy
[ PASSED  ] optane_check_dimm_capacity : All PMem Modules have the same capacity.
=======================================================================
Data analysis completed
=======================================================================
=======================================================================
Analysis Report Summary
=======================================================================
[ PASSED  ] = 8
[ FAILED  ] = 0
[ INFO    ] = 0
[ WARNING ] = 0
=======================================================================
PMem Checker Complete
Ended: Fri Jan 14 05:45:42 PM MST 2022
Duration: 55 seconds
Results: ./pmemchk.hostname.0114-1744
=======================================================================
```
