# perfSONAR-verify
Verify is a Perl script that compares perfSONAR data in remote perfSONAR hosts to the same data collected in centralized data stores. Its purpose is to confirm that the data centralization process is complete and accurate.

Three main tests are performed:
1) Alteration - This checks for items in both the central store and the remote perfSONAR hosts where measures differ.
2) Validation - This checks for items in the central store that are not in the remote perfSONAR hosts.
3) Coverage - This checks for items in the remote perfSONAR hosts that are not in the central store.

Typically, the script will be executed from cron, specifying a central repository to test with, a mesh or measurement archive to use, and the number of perfSONAR hosts to include in the test. The script will select the hosts, start, and end times at "random", perform the tests, and return results. It is also possible to specify the perfSONAR hosts to test against, along with start and end times. This is helpful when interested in repeating the same test.

Options:
--central - The host name or IP address of the central repository
--ma      - The measurement archive to select. Available archives:
            failures, histogram-owdelay, histogram-ttl, packet-count-lost, packet-count-sent, packet-loss-rate,
            packet-retransmits, packet-retransmits-subintervals, packet-trace, throughput, throughput-subintervals
--n       - The number of perfSONAR nodes to select for testing
--nodes   - Optional. A comma-separated list of perfSONAR host names or IP addresses to test with (if specific nodes are desired)
--start   - Optional. Select records with a time stamp >= this value
--end     - Optional. Select records with a time stamp <= this value
--action  - Optional. Select test results to display. Default action is to show all tests (either by not using it or with --action all) -- action validate => show alteration and validation; --action coverage => show alteration and coverage.

Sample Usage:
- Compare pack-count-sent data between the central store (psds1.grid.iu.edu) and two script-selected perfSONAR hosts that collect pack-count-sent data for a script-selected time range
verify --central psds1.grid.iu.edu --ma packet-count-sent --n 2

- Compare packet-loss-rate data between the central data store and one script-selected host, returning only alteration and validation differences; i.e., ignoring coverage test results
verify --ma packet-loss-rate --central psds-itb.grid.iu.edu --n 1 --action validate

- Compare packet-count-sent data from the central repository, using port 9090, with data from two specified hosts for the specified time range
verify --central fermicloud171.fnal.gov --port 9090 --ma packet-count-sent --n 2 --nodes sonar2.itim-cj.ro,perfsonar01.cmsaf.mit.edu --start 1434800261 --end 1434875281
