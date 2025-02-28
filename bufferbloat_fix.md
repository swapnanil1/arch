# Reducing Bufferbloat on Linux/Unix

Bufferbloat is a phenomenon that occurs when excessive buffering of packets causes high latency and poor network performance. This document outlines a series of steps to configure network settings on Linux/Unix systems to reduce bufferbloat effectively.

## Step 1: Set Queue Discipline Algorithm and List TCP Congestion Control Algorithms

```bash
sudo tc qdisc show dev enp34s0
```

2. **Set a New Queue Discipline**
   Set the queue discipline to `cake` with a bandwidth limit for example 90 Mbps (approximately 11.25 MBps). change to what your bandwidth is and enp34s0 is my network interface paste yours.
   
   ```bash
   sudo tc qdisc add dev enp34s0 root cake bandwidth 90Mbit
   ```

3. **List All Supported TCP Congestion Control Algorithms**
   
   ```bash
   sysctl net.ipv4.tcp_available_congestion_control
   ```

## Step 2: Make QDisk and MTU values persitent

1. View Current mtu settings (my default 1500)
   
   ```bash
   sudo ip link show dev enp34s0 | grep mtu
   ```

2. View Current Overhead settings (my preferred value 44 , set 34 as a starting point)
   
   ```bash
   sudo tc qdisc show dev enp34s0
   ```

3. Paste the below code to create a systemd service to apply qdisk
   
   ```systemd
   [Unit]
   Description=Traffic Shaping with Cake on enp34s0 
   After=network-online.target
   Wants=network-online.target
   
   [Service]
   Type=oneshot
   RemainAfterExit=yes
   ExecStart=/bin/sh -c "/sbin/tc qdisc del dev enp34s0 root 2>/dev/null; /sbin/tc qdisc add dev enp34s0 root cake bandwidth 90Mbit overhead 44 diffserv3 triple-isolate nonat nowash no-ack-filter split-gso rtt 50ms"
   ExecStop=/sbin/tc qdisc del dev enp34s0 root
   
   [Install]
   WantedBy=multi-user.target
   ```

## Step 3: Update sysctl.conf

Edit the `/etc/sysctl.conf` file to include the following settings for optimizing network performance and reducing bufferbloat. These settings will be applied at system startup or by executing `sysctl -p`.

### Bufferbloat and Network Performance Tuning

```bash
# TCP congestion control algorithm
net.ipv4.tcp_congestion_control = cubic

# Enable Explicit Congestion Notification (ECN)
net.ipv4.tcp_ecn=1

# Enable TCP Selective Acknowledgment (SACK) and Timestamps
net.ipv4.tcp_sack=1
net.ipv4.tcp_timestamps=1

# Set TCP Keepalive and Maximum Retransmission Attempts for SYN packets
net.ipv4.tcp_syn_retries=2
net.ipv4.tcp_synack_retries=2
net.ipv4.tcp_retries2=8

# Enables automatic tuning of receive buffers based on the link properties
net.ipv4.tcp_moderate_rcvbuf=1

# Configure TCP window scaling and buffer sizes
net.ipv4.tcp_window_scaling=1
net.core.rmem_max=31457280  # Maximum receive buffer size
net.core.wmem_max=31457280  # Maximum send buffer size
net.ipv4.tcp_rmem=4096 87380 31457280  # TCP receive buffer
net.ipv4.tcp_wmem=4096 65536 31457280  # TCP send buffer

# Set Minimum RTT window length
net.ipv4.tcp_min_rtt_wlen=300

# Disable slow start after idle
net.ipv4.tcp_slow_start_after_idle=0

# Prevent saving TCP metrics on closed connections
net.ipv4.tcp_no_metrics_save=1

# Enable TCP low latency
net.ipv4.tcp_low_latency=1

# Enable TCP MTU probing
net.ipv4.tcp_mtu_probing=1

# Set TCP Duplicate SACK option
net.ipv4.tcp_dsack=1

# Core network improvements
net.core.netdev_max_backlog=1000  # Limit the device queue size
```

## Step 4: Apply Settings in Terminal

1. **Set MTU Size for All Interfaces**
   
   ```bash
   echo "Setting MTU sizes for all interfaces..."
   sudo ip link set dev enp34s0 mtu 1500
   ```

2. **Disable Offloads like LSO and GRO**
   
   ```bash
   echo "Disabling offloads like LSO and GRO..."
   sudo ethtool -K enp34s0 tso off gso off gro off
   ```

3. **Disable Hardware Checksum and Segmentation Offloading**
   
   ```bash
   echo "Disabling hardware checksum and segmentation offloading..."
   sudo ethtool -K enp34s0 tx off rx off
   ```

## Important Notes

- Always ensure you have administrative privileges when executing these commands and modifying system files.
- The network interface names (e.g., `enp34s0`, `eth0`) must be adjusted according to your system's configuration. Check your interfaces using `ip link show`.
- When assessing performance improvements, monitor metrics such as latency and throughput to ensure these configurations yield the desired effects.

### Potential Issues

- If using `ethtool`, ensure it's installed on your system. Use the command `sudo apt install ethtool` if needed.
- The `tc` and `sysctl` changes may not take effect until the network service is restarted or the system is rebooted.
- Consider testing with different congestion control algorithms if "cubic" doesn't meet performance expectations. Other options include "bbr", "reno", and "vegas".

## Conclusion

Implementing these configurations can significantly reduce bufferbloat and improve overall network performance on Linux/Unix systems. Monitor your network's performance following these changes and adjust settings as necessary for optimal results.
