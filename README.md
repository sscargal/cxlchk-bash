# pmemchk

# Description 
`pmemchk` can be used to confirm a system with persistent memory is healthy and the configuration looks sane. pmemchk has two main modules:
- Collector - Collects configuration and health data from the host 
- Analyzer - Runs tests against the collected data and reports whether the test Passes or Fails. If a test fails, there should be some information to explain why.

# Help

```
Usage: ./pmemchk [optional args]

Checks for known issues with persistent memory configurations.
Run with root privilege.

Optional args:
   -A <Path to dataset>
      Specify the path to a dataset. Skips running the collector.

   -C
      Run the collector only. Skips running the analyzer.

   -c <Path to CXL executable>
      Specify the path to the CXL executable

   -h,-?
      Display this help

   -i <Path to IPMCTL executable>
      Specify the path to the IPMCTL executable

   -l
      List Analyzer Modules and Rules

   -m <module1, module2, ..., moduleN>
      Specify which Analyzer modules to include or exclude

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

Only collect data from the host. Does not run the analyzer
```
$ sudo ./pmemchk -C
```

Runs the analyzer against a previously collected dataset
```
$ sudo ./pmemchk -A ./pmemchk.hostname.0113-1210
```

Specifies the location of the `ipmctl`,`ndctl`, and `cxl` utilities if they are not in the root users PATH
```
$ sudo ./pmemchk -c /usr/local/bin/cxl -i /usr/local/bin/ipmctl -n /usr/local/bin/ndctl
```

List the available Analyzer Modules and Rules
```
$ sudo ./pmemchk -l
=======================================================================
Starting PMem Checker
pmemchk Version 0.1.0
Started: Tue Feb 15 03:56:52 PM MST 2022
=======================================================================
Analyzer Modules
================
linux
optane

Analyzer Rules
==============
linux/parse_messages
analyzer/optane/check_region_persistentmemorytype
analyzer/optane/check_region_freecapacity
analyzer/optane/check_dimm_lockstate
analyzer/optane/check_dimm_firmware_version
analyzer/optane/check_dimm_health_status
analyzer/optane/check_dimm_arsstatus
analyzer/optane/check_percentage_remaining
analyzer/optane/check_dimm_population
analyzer/optane/check_region_capacity
analyzer/optane/check_region_health
[...snip...]
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
