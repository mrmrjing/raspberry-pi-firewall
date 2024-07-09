# Raspberry Pi Firewall Project

## Objective
To configure a secure and efficient firewall on a Raspberry Pi 5 using `iptables` to control and filter incoming and outgoing network traffic.

## Skills Demonstrated
- Linux system administration
- Network security
- Firewall configuration
- Scripting and automation

## Tools and Technologies
- Raspberry Pi 5
- Raspberry Pi OS
- `iptables`

## Project Steps

1. **Initial Setup**:
   - Install Raspberry Pi OS on the Raspberry Pi 5.
   - Update the system packages.

     ```bash
     sudo apt update
     sudo apt upgrade -y
     ```

2. **Install `iptables`**:
   - `iptables` is pre-installed on most Linux distributions, including Raspberry Pi OS. Verify its installation:

     ```bash
     sudo iptables -V
     ```

3. **Basic Firewall Configuration**:
   - Flush existing rules to start with a clean slate.

     ```bash
     sudo iptables -F
     ```

   - Set default policies to drop all incoming and forwarding traffic, and accept outgoing traffic.

     ```bash
     sudo iptables -P INPUT DROP
     sudo iptables -P FORWARD DROP
     sudo iptables -P OUTPUT ACCEPT
     ```

   - Allow loopback traffic (internal communication within the system).

     ```bash
     sudo iptables -A INPUT -i lo -j ACCEPT
     sudo iptables -A OUTPUT -o lo -j ACCEPT
     ```

   - Allow established and related incoming traffic.

     ```bash
     sudo iptables -A INPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT
     ```

   - Allow SSH, HTTP, and HTTPS traffic.

     ```bash
     sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT # Allow SSH (port 22)
     sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT # Allow HTTP (port 80)
     sudo iptables -A INPUT -p tcp --dport 443 -j ACCEPT # Allow HTTPs (port 443)
     ```

   - Allow DNS traffic. 

     ```bash
     sudo iptables -A INPUT -p udp --sport 53 -j ACCEPT
     sudo iptables -A INPUT -p tcp --sport 53 -j ACCEPT
     ```

4. **Save and Persist Firewall Rules**:
   - Create the directory to store `iptables` rules.

     ```bash
     sudo mkdir -p /etc/iptables
     ```

   - Save the current `iptables` rules.

     ```bash
     sudo sh -c "iptables-save > /etc/iptables/rules.v4"
     ```

   - Create a systemd service to restore `iptables` rules on boot.

     ```bash
     sudo nano /etc/systemd/system/iptables-restore.service
     ```

   - Add the following content to the service file:

     ```ini
     [Unit]
     Description=Restore iptables rules
     Before=network-pre.target
     Wants=network-pre.target
     DefaultDependencies=no

     [Service]
     Type=oneshot
     ExecStart=/sbin/iptables-restore /etc/iptables/rules.v4
     ExecStart=/sbin/ip6tables-restore /etc/iptables/rules.v6

     [Install]
     WantedBy=multi-user.target
     ```

   - Enable the service to run on boot.

     ```bash
     sudo systemctl enable iptables-restore.service
     ```

5. **Testing and Verification**:
   - Reboot the Raspberry Pi to ensure the `iptables` rules are applied correctly.

     ```bash
     sudo reboot
     ```

   - After reboot, verify the `iptables` rules are in place.

     ```bash
     sudo iptables -L -v -n
     ```

## Outcome
A secure and efficient firewall was successfully configured on a Raspberry Pi 5 using `iptables`. This setup ensures that only necessary traffic is allowed, enhancing the security of the system by blocking unwanted and potentially harmful connections.

## Reflection
This project provided valuable experience in Linux system administration, network security, and firewall configuration. The skills and knowledge gained from this project are directly applicable to real-world cybersecurity scenarios, making it a strong addition to my resume and portfolio.
