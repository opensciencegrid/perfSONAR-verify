# perfSONAR-verify
Verify is a Perl script that compares perfSONAR data in remote perfSONAR hosts to the same data collected in centralized data stores. Its purpose is to confirm that the data centralization process is complete and accurate.

Three main tests are performed:  
1) Alteration - This checks for items in both the central store and the remote perfSONAR hosts where measures differ.  
2) Validation - This checks for items in the central store that are not in the remote perfSONAR hosts.  
3) Coverage - This checks for items in the remote perfSONAR hosts that are not in the central store.

Typically, the script will be executed from cron, specifying a central repository to test with, a mesh or measurement archive to use, and the number of perfSONAR hosts to include in the test. The script will select the hosts, start, and end times at "random", perform the tests, and return results. It is also possible to specify the perfSONAR hosts to test against, along with start and end times. This is helpful when interested in repeating the same test.

Options:  
--central    - The host name or IP address of the central repository  
--mesh       - The mesh to use  
--mesh_menu  - Allows selection of a mesh from a list. Helpful when the mesh name is not known.  
--test       - The test type to select. Available types are:  
               failures, histogram-owdelay, histogram-ttl, packet-count-lost, packet-count-sent, packet-loss-rate,  
               packet-retransmits, packet-retransmits-subintervals, packet-trace, throughput, throughput-subinterval  
--test_menu  - Allows selection of a test type from a list. Helpful when the test name is not known.  
--n          - The number of perfSONAR nodes to select for testing  
--nodes      - Optional. A comma-separated list of perfSONAR host names or IP addresses to test with  
               (if specific nodes are desired)  
--start      - Optional. Select records with a UTC epoch seconds time stamp >= this value  
--end        - Optional. Select records with a UTC epoch seconds time stamp <= this value  
--start_date - Optional. Like start but uses a UTC date with format [M]M/[D]D/YY[YY][,[h]h[:[m]m[:[s]s]]]  
--end_date   - Optional. Like end but uses a UTC date with format [M]M/[D]D/YY[YY][,[h]h[:[m]m[:[s]s]]]  
--display     - Optional. Select test results to display. Default action is to show all tests  
                (either by not using it or with --display all)  
               --display validate => show alteration and validation;  
               --display coverage => show alteration and coverage.  
--update     - Optional. Mesh, node, and IP address information is updated regularly by update scripts in this repository.  
               This option forces an update to occur prior to running verify. Note: The update may take a few minutes.  

Sample Usage:  
- Compare pack-count-sent data between the central store (psds1.grid.iu.edu) and two script-selected perfSONAR hosts that collect pack-count-sent data for a script-selected time range  
verify --central psds1.grid.iu.edu --test packet-count-sent --n 2  

- Compare packet-loss-rate data between the central data store and one script-selected host, returning only alteration and validation differences; i.e., ignoring coverage test results  
verify --test packet-loss-rate --central psds-itb.grid.iu.edu --n 1 --display validate  

- Compare packet-count-sent data from the central repository, using port 9090, with data from two specified hosts for the specified time range  
verify --central fermicloud171.fnal.gov --port 9090 --test packet-count-sent --n 2 --nodes   sonar2.itim-cj.ro,perfsonar01.cmsaf.mit.edu --start 1434800261 --end 1434875281  

- Request a comparison with specific perfSONAR hosts in a given mesh for a specified time range  
verify --central fermicloud171.fnal.gov --port 9090 --mesh 'USATLAS Bandwidth Mesh Test' --test throughput-subintervals --nodes psum02.aglt2.org,ps2.ochep.ou.edu --start_date 07/02/2015,05:29:28 --end_date 07/02/2015,10:52:01 --display all  

- Allow user to select a test type and mesh from a list  
verify --central fermicloud171.fnal.gov --port 9090 --mesh_menu --test_menu -n 2  
