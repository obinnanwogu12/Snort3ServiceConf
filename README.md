# Snort3ServiceConf
Snort 3 configuration files for running Snort as a system service on Linux.

## Disable NIC Offloading for Snort 3

To prevent Snort 3 from truncating large packets (maximum packet size of 1518 bytes), disable Generic Receive Offload (GRO) and Large Receive Offload (LRO) on the network interface and to ensure these settings persist after a reboot, create a systemd service.

### 1. Create the Service File

Create the service file:

```bash
sudo nano /etc/systemd/system/snort3-nic.service
```

### 2. Add the Service Configuration

Add the following configuration:

```ini
[Unit]
Description=Set Snort 3 NIC in promiscuous mode and disable GRO/LRO on boot
After=network.target

[Service]
Type=oneshot
ExecStart=/usr/sbin/ip link set dev ensp03 promisc on
ExecStart=/usr/sbin/ethtool -K ensp03 gro off lro off
TimeoutStartSec=0
RemainAfterExit=yes

[Install]
WantedBy=default.target
```


## Create a Snort 3 Systemd Service

To run Snort 3 automatically as a background service at system startup, create a `systemd` service unit.

### 3. Create the Service File

Create a new systemd service file:

```bash
sudo nano /etc/systemd/system/snort3.service
```

### 4. Add the Service Configuration

Paste the following configuration into the file:

```ini
[Unit]
Description=Snort 3 NIDS Daemon
After=syslog.target network.target

[Service]
Type=simple
ExecStart=/usr/local/bin/snort -c /usr/local/etc/snort/snort.lua -i ensp03 -A alert_fast -l /var/log/snort/ -D -s 65535 -k none

[Install]
WantedBy=multi-user.target
```
