# perfSONAR-verify
Scripts to help identify possible discrepancies between data in perfSONAR hosts and in centralized data stores.

Sample usage
verify --central psds1.grid.iu.edu --ma throughput --n 12
verify --central psds-itb.grid.iu.edu --ma histogram-owdelay --n 4
verify --central fermicloud035.fnal.gov --port 9090 --ma histogram-owdelay --n 4
Requesting specific perfSONAR hosts; --action all => show Alteration, Validation, and Coverage differences
verify --central psds.grid.iu.edu --ma throughput --n 2 --nodes 193.109.172.188,psum02.aglt2.org --start 142989031 --end 143051431 --action all

Sample usage from cron
10 * * * * root /root/dev/gen/bin/verify --ma throughput --central psds1.grid.iu.edu --n 24 >> /root/dev/gen/logs/throughput_psds1_verify.log 2>&1
15 * * * * root /root/dev/gen/bin/verify --ma throughput --central psds-itb.grid.iu.edu --n 24 >> /root/dev/gen/logs/throughput_psds-itb_verify.log 2>&1
30 * * * * root /root/dev/gen/bin/verify --ma throughput --central fermicloud035.fnal.gov --port 9090 --n 24 >> /root/dev/gen/logs/throughput_fermicloud_verify.log 2>&1
35 * * * * root /root/dev/gen/bin/verify --ma histogram-owdelay --central psds1.grid.iu.edu --n 8 >> /root/dev/gen/logs/owdelay_psds1_verify.log 2>&1
40 * * * * root /root/dev/gen/bin/verify --ma histogram-owdelay --central psds-itb.grid.iu.edu --n 8 >> /root/dev/gen/logs/owdelay_psds-itb_verify.log 2>&1
45 * * * * root /root/dev/gen/bin/verify --ma histogram-owdelay --central fermicloud035.fnal.gov --port 9090 --n 8 >> /root/dev/gen/logs/owdelay_fermicloud_verify.log 2>&1
