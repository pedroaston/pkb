
## On Ubuntu

Ubuntu has its own daemon for time sync called: timedatectl. This daemon provides a low level of time sync.

Command output:
```sh
augustus@homeserver:~$ timedatectl
               Local time: Sat 2024-10-05 15:23:57 UTC
           Universal time: Sat 2024-10-05 15:23:57 UTC
                 RTC time: Sat 2024-10-05 15:23:57
                Time zone: Etc/UTC (UTC, +0000)
System clock synchronized: yes
              NTP service: n/a
          RTC in local TZ: no

```
To have an higher time sync we need to deactivate this daemon NTP and install ntp.
```sh
 sudo timedatectl set-ntp no
 sudo apt install ntp
 augustus@homeserver:~$ ntpq -p
     remote                                   refid      st t when poll reach   delay   offset   jitter
=======================================================================================================
 0.ubuntu.pool.ntp.org                   .POOL.          16 p    -  256    0   0.0000   0.0000   0.0001
 1.ubuntu.pool.ntp.org                   .POOL.          16 p    -  256    0   0.0000   0.0000   0.0001
 2.ubuntu.pool.ntp.org                   .POOL.          16 p    -  256    0   0.0000   0.0000   0.0001
 3.ubuntu.pool.ntp.org                   .POOL.          16 p    -  256    0   0.0000   0.0000   0.0001
+prod-ntp-4.ntp4.ps5.canonical.com       183.160.133.132  2 u   58   64   37  30.1872  -2.0102   2.5676
+a85-138-124-211.cpe.netcabo.pt          195.22.17.7      2 u   50   64   37  19.2779  -0.7384   3.8830
+ntp.leitecastro.com                     194.58.204.148   2 u   53   64   37   3.8292  -1.9826   3.4514
+time.cloudflare.com                     10.107.8.4       3 u   51   64   37   3.3872  -1.3382   3.4297
+smtp-in1.aqea.net                       194.117.47.44    3 u   46   64   37   4.8166  -1.7131   3.2479
+ntp1.tecnico.ulisboa.pt                 56.99.239.27     2 u   53   64   37   3.3657  -1.8023   3.3589
+time.cloudflare.com                     10.107.8.4       3 u   50   64   37   3.5308  -1.3158   3.3246
+ntp.rnl.tecnico.ulisboa.pt              193.147.107.33   2 u   50   64   37   6.0292  -0.5580   3.8686
+ntp3.leitecastro.com                    192.36.143.150   2 u   45   64   37   4.3875  -1.8151   2.9481
+ntp4.leitecastro.com                    194.58.204.148   2 u   49   64   37   4.4199  -1.6646   2.9445
+ntp.leitecastro.com                     194.58.204.148   2 u   47   64   37   4.3473  -1.7720   2.9149
+ntp4.leitecastro.com                    194.58.204.148   2 u   46   64   37   3.6988  -1.9555   3.0596
+ntp.rnl.tecnico.ulisboa.pt              193.147.107.33   2 u   46   64   37   6.1122  -1.1316   3.4822
*ntp04.oal.ul.pt                         150.214.94.5     2 u   46   64   37   3.7275  -1.7538   3.2601
+ntp3.leitecastro.com                    192.36.143.150   2 u   46   64   37   3.8009  -2.2815   2.6893
+ntp02.oal.ul.pt                         150.214.94.5     2 u   42   64   37   4.0974  -1.8058   3.0099
+ntp2.leitecastro.com                    194.58.204.148   2 u   43   64   37   3.7655  -2.1972   2.7738
```

I implemented this on my server, because I had kentik synthetic agents running there.

## Tasks

1. Understand the error currently existent in the server
2. Understand if a docker container uses the host time sync or does some error propagate.

