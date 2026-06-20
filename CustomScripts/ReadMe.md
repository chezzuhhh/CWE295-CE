CustomScripts/ contains a few scripts used for various purposes while discovering this vuln. These scripts even came in handy going beyond CWE-295, and leveraging it to discover other vulnerabilities, and I hope these tools also come in handy for anyone interested in recreating this PoC or analyzing similar IoT Network traffic.


## mitmToJson.py
Converts `.mitm` capture files into a readable JSON format.

#### Info/Flow:
    Opens a `.mitm` file and uses `mitmproxy.io` to read the captured flows.
    Iterates through the stream, extracting the raw state data from each intercepted flow.
    Passes the data through `MitmJSONEncoder` and attempts to decode bytes to UTF-8 readable strings.Falls back to base64 if Unicode decode error occurs.
    Writes parsed dictionary to a formatted JSON file.

#### Usage:
`python mitmToJson.py <input.mitm> <output.json>`


## AccessPoint.sh
Configures a wireless interface (`wlan0`) to act as a routed access point for traffic interception. 

#### Info/Flow:
    Manually assigns a static IP (`192.168.100.1/24`) to `wlan0` and brings the interface up.
    Enables IPv4 forwarding in the kernel and configures `iptables` to masquerade outbound traffic from the AP through the host's primary connection.
    Contains commented-out `iptables` rules that can be used to establish a transparent proxy, redirecting all port 80/443 traffic to `mitmproxy'.

#### Usage:
`chmod +x AccessPoint.sh`
`sudo ./AccessPoint.sh`


## pcapParser.py
Parses `.pcapng` network capture files, extracting a specific set of protocol fields into CSV format for easier bulk analysis.

#### Info/Flow:
    Searches the current working directory for all `.pcapng` files.
    Uses a predefined list of Wireshark/tshark display filters to target specific data points across L2/L3/L4, DNS, HTTP, TLS (including leaf certificates and ciphersuites), STUN/WebRTC, and MQTT payloads.
    Executes a `tshark` subprocess for each `pcap` file, forcing the output format to a comma-separated, quote-enclosed CSV with headers.
    Generates a corresponding `_extract.csv` file for every parsed capture, calculating and printing the final file size upon completion.

#### Usage:
*(Ensure `tshark` is installed before running)*
`python pcapParser.py`
