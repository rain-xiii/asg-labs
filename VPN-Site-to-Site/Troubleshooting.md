## Issue: Tunnel is Not Up

If the VPN tunnel is not in the "Up" state, try the following steps:

1. **Restart Network Services**  
   Run the following commands to restart the network and IPSec services:
   ```bash
   sudo service network restart
   sudo service ipsec restart
   ```

2. **Amazon Linux Specific Configuration**  
    If you are using Amazon Linux, open the VPN configuration file and locate the sections for "Conn Tunnel 1" and "Conn Tunnel 2".
    - **Action:**
        - Remove the line containing: `auth=esp`
    - **Explanation**:

        - On Amazon Linux, the VPN software is set up to choose the best settings automatically. Keeping the line auth=esp can force the system to use a setting that might conflict with these automatic choices, preventing the tunnel from coming up. By removing this line, you allow the system to use its default settings, which usually work better.

## Issue: Unable to Ping the Private Subnet

- **Action:**  
    Check the Route Table associated with the Private Subnet to ensure that a route to the NAT Gateway is properly configured.

---

_Note: If the problem persists after performing these steps, review your VPN and firewall configurations for any additional issues._