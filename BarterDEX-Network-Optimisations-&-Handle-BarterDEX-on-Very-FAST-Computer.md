On very fast system `nanomsg` can crash. The crash is due to socket exhaustion, ie. no more sockets in the system due to the system goes so fast. It has to do with the coins loop saturating things and using up all sockets. Nanomsg assues it can get an open socket. The following magic TCP settings change will get sockets to recycle as fast as possible.

The workaround is to create a new conf file and make it run on every boot. You can set them with `sysctl -w`, but to make them permanent, better put them in a file like `/etc/sysctl.d/01-barterdex.conf` and execute `sudo sysctl -p /etc/sysctl.d/01-barterdex.conf` afterwards which will set both parameters.

*Note:* `root` or `sudo` needed for these commands. If you are total `N00BS` and not sure what these lines do, you shouldn't mess up with these settings.

To create the conf file use `sudo nano /etc/sysctl.d/01-barterdex.conf` and paste the following inside the conf file and save it using `CTRL+X` then `Y` and `Enter`.

Contents of `01-barterdex.conf` file:

```shell
net.core.rmem_max = 33554432
net.core.wmem_max = 33554432
net.ipv4.tcp_rmem = 4096 87380 33554432
net.ipv4.tcp_wmem = 4096 65536 33554432
net.core.netdev_max_backlog = 262144
net.ipv4.tcp_no_metrics_save = 1
net.ipv4.tcp_tw_reuse = 1
net.ipv4.tcp_max_orphans = 262144
net.ipv4.tcp_max_syn_backlog = 262144
net.ipv4.tcp_fin_timeout = 10
net.ipv4.tcp_keepalive_time = 60
net.ipv4.tcp_keepalive_intvl = 10
net.ipv4.tcp_keepalive_probes = 3
net.ipv4.tcp_synack_retries = 2
net.ipv4.tcp_syn_retries = 2
net.ipv4.ip_local_port_range = 16001 65530
net.core.somaxconn = 20480
net.ipv4.tcp_max_tw_buckets = 2000000
```

You can also use a kernel module dealing with network congestion. But, there is not enough data on how much it helps. For this you need to add the following 2 lines into the previous file.

```shell
net.core.default_qdisc=fq
net.ipv4.tcp_congestion_control=bbr
```

And, for the `bbr` module in the last line to be automatically loaded on boot, put the following line in `/etc/modules-load.d/modules.conf` file.

```shell
tcp_bbr
```

Reboot your computer/server.