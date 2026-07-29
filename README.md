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

### 4. Enable the Service

Reload the systemd daemon:

```bash
sudo systemctl daemon-reload
```

Enable the service to start automatically on boot:

```bash
sudo systemctl enable snort3-nic.service
```

Start the service immediately:

```bash
sudo systemctl start snort3-nic.service
```

### 5. Verify the Service

Check the service status:

```bash
sudo systemctl status snort3-nic.service
```


## Create a Snort 3 Systemd Service

To run Snort 3 automatically as a background service at system startup, create a `systemd` service unit.

### 6. Create the Service File

Create a new systemd service file:

```bash
sudo nano /etc/systemd/system/snort3.service
```

### 7. Add the Service Configuration

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

### 8. Reload Systemd

Reload the systemd daemon to recognize the new service:

```bash
sudo systemctl daemon-reload
```

### 9. Enable the Service

Enable the service so it starts automatically on boot:

```bash
sudo systemctl enable snort3.service
```

### 10. Start the Service

Start the Snort 3 service immediately:

```bash
sudo systemctl start snort3.service
```

### 11. Verify the Service Status

Check that the service is running successfully:

```bash
sudo systemctl status snort3.service
```
