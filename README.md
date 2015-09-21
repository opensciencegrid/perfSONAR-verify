# perfSONAR-verify
Verify is a Perl script that compares perfSONAR data in remote perfSONAR hosts to the same data collected in centralized data stores. Its purpose is to confirm that the data centralization process is complete and accurate.

#### Comparisons Performed  
1) Alteration - This checks for items in both the central store and the remote perfSONAR hosts where values differ.  
2) Validation - This checks for items in the central store that are not in the remote perfSONAR hosts.  
3) Coverage - This checks for items in the remote perfSONAR hosts that are not in the central store.

Typically, the script will be executed from cron, specifying a central repository to test with, a mesh and/or test type to use, and the number of perfSONAR hosts to include in the test. The script will select the hosts, start, and end times at "random", perform the tests, and return results. It is also possible to specify the perfSONAR hosts to test against, along with start and end times. This is helpful when interested in repeating the same test.

#### Options
```
--central    - Comma-separated string. The host name or IP address of one or more central repositories, each optionally
               followed by a colon (:) and a port number. Default port number is 80. Alternatively, use 'all' to select
               all central repositories, in which case the host names and ports are obtained from the configuration file. 
--mesh       - String. The mesh to use  
--mesh_menu  - Integer. Allows selection of a mesh from a list. Helpful when the mesh name is not known. 
--test       - String. The test type to select. Available types are: 
               failures, histogram-owdelay, histogram-ttl, packet-count-lost, packet-count-sent, packet-loss-rate, 
               packet-retransmits, packet-retransmits-subintervals, packet-trace, throughput, throughput-subinterval 
--test_menu  - Integer. Allows selection of a test type from a list. Helpful when the test name is not known. 
--n          - Integer. The number of perfSONAR nodes to select for testing. Ignored when --nodes is used. 
--nodes      - Optional. A comma-separated list of perfSONAR host names or IP addresses to test with 
               (if specific nodes are desired) 
--start      - Optional integer. Select records with a UTC epoch seconds time stamp >= this value
--end        - Optional integer. Select records with a UTC epoch seconds time stamp <= this value
--start_date - Optional. Like start but uses a UTC date string with format [M]M/[D]D/YY[YY][,[h]h[:[m]m[:[s]s]]]
--end_date   - Optional. Like end but uses a UTC date string with format [M]M/[D]D/YY[YY][,[h]h[:[m]m[:[s]s]]]
--last       - Optional. Designates the last so many time units as the start time. End time is "now".
               Possible values are in the form of nw, nd, nh, nm, or ns, where n is a positive integer and
               w = weeks, d = days, h = hours, m = minutes, s = secs.
--delay      - Optional. Skip records with time stamps more recent than this many seconds ago. Default value is 
               stored in the conf file. This gives time for the central store to sync up with remote stores and 
               reduce false positives. It is ignored when start/end parameters are specified. It is referenced 
               otherwise. 
--display    - Optional string. Select test results to display. Default action is to show all tests 
               (either by not using it or with --display all) 
               --display validate => show alteration and validation 
               --display coverage => show alteration and coverage 
--out        - Optional path and file name prefix for collected data used in generating the log. Remote data is written to 
               "path/prefix-[timestamp].remote"; the other file names take the format "path/prefix-[host]-[timestamp].[source]". 
               The central store short name goes in [host], [timestamp] refers to the UNIX time the script ran and [source] is 
               "central" for the central store, or "diff" for the remote/central comparison. 
--split_logs - Optional. The verify script generally outputs results to STDOUT. Use this option when it is desired to output 
               results to different log files, each corresponding to a central store identified in the --central option. 
--update     - Optional. Mesh, node, and IP address information is updated regularly by update scripts in this repository. 
               This option forces an update to occur prior to running verify. Note: The update may take a few minutes to 
               complete and is meant to be used sparingly. 
--help       - Displays this information 
```

#### Sample Usage  
- Compare pack-count-sent data between the central store (psds1.grid.iu.edu) and two script-selected perfSONAR hosts that collect pack-count-sent data for a script-selected time range. Output results to STDOUT. 
```
verify --central psds1.grid.iu.edu --test packet-count-sent --n 2
```

- Compare packet-loss-rate data between the central data store and one script-selected host, returning only alteration and validation differences; i.e., ignoring coverage test results to STDOUT. 
```
verify --test packet-loss-rate --central psds2.grid.iu.edu --n 1 --display validate
```

- Compare packet-count-sent data from the central repository, using port 9090, with data from two specified hosts for the specified time range  
```
verify --central fermicloud171.fnal.gov:9090 --test packet-count-sent --nodes sonar2.itim-cj.ro,perfsonar01.cmsaf.mit.edu --start 1434800261 --end 1434875281
```

- Request a comparison with specific perfSONAR hosts in a given mesh for a specified time range  
```
verify --central fermicloud171.fnal.gov:9090 --mesh 'USATLAS Bandwidth Mesh Test' --test throughput-subintervals --nodes psum02.aglt2.org,ps2.ochep.ou.edu --start_date 09/12/2015,05:29:28 --end_date 09/12/2015,10:52:01
```

- Compare throughput data between all central stores (identified it the config file) and four script-selected perfSONAR hosts which collect throughput data in the given mesh for a script-selected time range. Store raw data in files with prefix ../logs/vtest and output final results to separate throughput log files, according to the selected central stores. 
```
verify -central all --mesh 'USATLAS Bandwidth Mesh Test' --test throughput -n 4 --out ../logs/vtest --split_logs
```
   
- Compare failures data between two specified central hosts and three script-selected perfSONAR hosts which collect failures data in the given mesh for a script-selected time range. Output results to STDOUT. 
```
verify -central fermicloud171.fnal.gov:9090,psds1.grid.iu.edu --mesh 'USATLAS Bandwidth Mesh Test' --test throughput -n 3
```

- Allow user to select a test type and mesh from option lists  
```
verify --central fermicloud171.fnal.gov:9090 --mesh_menu --test_menu -n 2
```

#### Installation
Set up subdirectories /bin, /conf, /data under a parent directory. Place executables under /bin, the conf file under /conf.
There are two update executables, update_meshes and update_nodes, that will build two reference files under /data.
Add the update scripts to cron to run at low frequency, probably once a day is enough.
Add the verify script to cron to run frequently with whatever parameters are appropriate for the desired testing.
