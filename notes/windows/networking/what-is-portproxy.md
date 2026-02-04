# What is `portproxy`?

In Windows networking, `portproxy` is a sub-command of the **Netsh** (Network Shell) utility. It acts as a built-in tool for **port forwarding**, allowing the operating system to listen for incoming traffic on a specific IP address and port and redirect it to a different IP and port.

This is particularly useful when you need to bridge traffic between different network interfaces or manage connections for virtual environments.

## Core Functionality

The most common use for `portproxy` is setting up a **v4tov4** proxy. This takes traffic coming into your machine on one IPv4 port and sends it to another.

### Why Developers Use It

For someone working on web applications, `portproxy` is a lifesaver in these specific scenarios:

* **WSL2 Access:** Since Windows Subsystem for Linux (WSL2) runs on a virtualized network, its "localhost" isn't always the same as your Windows "localhost." You can use `portproxy` to map your Windows IP to the WSL2 IP so external devices on your Wi-Fi can see your dev server.
* **Docker Containers:** Similar to WSL, it helps route traffic from a physical network adapter into a containerized service.
* **Legacy Redirection:** If you have a hardcoded application looking for a service on `port 8080`, but your new service is running on `port 3000`, `portproxy` can bridge that gap without you changing a single line of code.

### Basic Command Structure

To set up a proxy, you run the command in an **Administrator** command prompt. Here is the standard syntax:

```bash
netsh interface portproxy add v4tov4 listenport=[LOCAL_PORT] listenaddress=[LOCAL_IP] connectport=[TARGET_PORT] connectaddress=[TARGET_IP]

```

**Common Management Commands:**

* **View all proxies:** `netsh interface portproxy show all`
* **Clear a specific proxy:** `netsh interface portproxy delete v4tov4 listenport=8080 listenaddress=0.0.0.0`
* **Reset everything:** `netsh interface portproxy reset`

## Key Technical Details

* **Protocol Limitation:** `portproxy` only supports **TCP** traffic. If you need to forward UDP (commonly used in gaming or certain streaming protocols), you'll need a different tool or a third-party firewall.
* **Firewall Requirement:** Even if the proxy is set up correctly, you must ensure the Windows Defender Firewall allows traffic through the `listenport`, or the connection will be dropped before it hits the proxy.
