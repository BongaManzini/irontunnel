# IronTunnel: Fast C++ Packet Sniffer & Python Defense Daemon ️

> **Security Summit ITWeb Hackathon Project** 
> **Challenge:** "Network Router Firewall and Management — A system that can be adopted into a live business." 
> **Deliverable:** A network monitoring dashboard and automated defense system that manages and protects devices connected to an enterprise router. 
> **Author:** Bonga Manzini (UNISA BSc Informatics Student)

---

## Why I Built This

When an attacker tries to poison ARP tables or flood a network with SYN packets, passive packet sniffers just watch it happen without stopping it. By the time a human network admin notices, the server is already down or credentials are stolen.

I wanted to build a tool that does both:
1. **Sniffs packets at wire speed in C++** so it never drops packets during high traffic.
2. **Automatically blocks the attacker in Python** by creating instant firewall rules (`iptables` / `nftables`) in under a second.

---

## How It Is Organized

```
IronTunnel/
├── packet_engine_cpp/      # Fast C++ packet sniffer using raw sockets & WinPcap
│  ├── include/         # Packet header definitions and structs
│  ├── src/           # Low-level sniffer and frame parser
│  ├── CMakeLists.txt      # CMake build file
│  └── main.cpp         # Main entry point
│
├── autonomic_defense/      # Python threat detector and auto-firewall daemon
│  ├── src/           # Scapy flow analyzer and firewall blocker
│  ├── scripts/         # Setup automation
│  ├── tests/          # Unit and integration test suite
│  └── requirements.txt     # Python dependencies
│
└── README.md           # This guide
```

---

## How It Works Step-by-Step

```
[Network Traffic]
       │
       ▼
[C++ Packet Engine] ──► Captures raw packets with zero lag using raw sockets
       │
       ▼
[Python Daemon]   ──► Checks for attacks (ARP poisoning, SYN floods, rogue DHCP)
       │
       ▼  (Attack Detected!)
[Firewall Action]  ──► Automatically runs: iptables -A INPUT -s <attacker_ip> -j DROP
                         (The attacker is blocked in milliseconds!)
```

---

## What Attacks It Catches

| Attack Type | How It Detects It | What IronTunnel Does Automatically |
| :--- | :--- | :--- |
| **ARP Spoofing (MITM)** | Catches duplicate IP-to-MAC replies | Drops fake ARP packets and restores the real gateway MAC address |
| **TCP SYN Flood** | Counts incomplete handshakes per second | Rate-limits the attacking IP and adds a temporary firewall drop rule |
| **Rogue DHCP Server** | Flags unauthorized DHCP offer replies | Blocks the rogue DHCP server port immediately |
| **Port Scanning** | Detects rapid SYN probes across ports | Temporarily blacklists the scanning IP |

---

## How to Build and Run

### 1. Build the C++ Packet Sniffer
```bash
cd packet_engine_cpp
mkdir build && cd build
cmake ..
make
```

### 2. Run the Python Defense Daemon
```bash
cd ../../autonomic_defense
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
python3 src/main.py
```

---

## Contributing & Pull Requests

Pull requests and issues are warmly welcomed!
- Want to add a new attack detection signature or improve C++ socket handling?
  1. Fork this repository
  2. Create a branch (`git checkout -b feature/better-detection`)
  3. Commit with clear comments
  4. Submit a Pull Request!
