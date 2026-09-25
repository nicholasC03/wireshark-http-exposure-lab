# Wireshark HTTP Exposure Lab

## Purpose

This project demonstrates how plaintext HTTP request data can be observed in a controlled packet capture. I ran both a curl client and a Python HTTP server inside an Ubuntu Desktop virtual machine and captured their communication on the Linux loopback interface with Wireshark. The request contained the fictional marker `DEMO_ONLY_123` so I could verify whether application-layer request data was readable in plaintext.

## Lab diagram

```text
curl client (Ubuntu VM)
        |
        | HTTP over TCP
        v
127.0.0.1:8000
Python HTTP server
        ^
        |
Wireshark observing interface lo

Both the client and server ran inside the same Ubuntu VM. Wireshark captured the traffic on the VM's loopback (lo) interface.
Tools and settings
- Ubuntu Desktop VM
- Wireshark 4.6.4
- Python 3.14.4
- Python HTTP server bound to 127.0.0.1:8000
- Capture interface: lo
- Capture filter: tcp port 8000
- TCP stream: 0
- Capture date: September 24, 2026, EDT
The experiment used only locally generated traffic and a fictional marker. No real credentials, API keys, or tokens were used.
Reproduce
The complete lab procedure is documented in [`breakdown.md`](breakdown.md).
Start the local Python web server:
python3 -m http.server 8000 --bind 127.0.0.1 --directory lab_site

With Wireshark capturing the lo interface using the capture filter:
tcp port 8000

send the marked HTTP request:
curl --noproxy '*' --http1.1 -i 'http://127.0.0.1:8000/?marker=DEMO_ONLY_123'

Stop the capture immediately afterward and save it as:
captures/http-loopback-demo.pcapng

Findings
The saved capture contained the TCP connection setup, the marked HTTP request, and the HTTP response.
TCP connection
The TCP three-way handshake for the captured connection appeared in:
- Frame 2: SYN
- Frame 3: SYN+ACK
- Frame 4: ACK
 
Plaintext HTTP request
Frame 5 contained the HTTP GET request.
The request target was:
/?marker=DEMO_ONLY_123

Wireshark decoded the request and displayed the fictional marker directly in the HTTP request data.
 
HTTP response
Frame 9 contained the server response:
404 Not Found

The response status does not change the main observation of this experiment: the HTTP request itself, including its query-string marker, was transmitted in readable plaintext.
 
Follow TCP Stream
Following TCP stream 0 showed the application-layer conversation in readable form. The request included DEMO_ONLY_123, demonstrating that the marker was visible to an observer capable of capturing this local traffic.
 
The complete packet capture is available here:
[Download the lab capture](captures/http-loopback-demo.pcapng)
Detailed packet observations are recorded here:
[Read the packet observations](notes/observations.md)
Why it matters
HTTP does not encrypt its application data. In this capture, Wireshark could directly display the request target and the fictional marker DEMO_ONLY_123.
A party able to observe comparable cleartext HTTP traffic could therefore read request data sent without transport encryption. Real passwords, session tokens, API keys, or other sensitive values should not be placed in URL query parameters. Real services should use HTTPS along with appropriate application authentication and authorization controls.
Limits
This experiment was intentionally narrow.
- All traffic was generated locally inside one Ubuntu VM.
- The capture used the Linux loopback interface.
- Both source and destination were 127.0.0.1.
- The marker DEMO_ONLY_123 was fictional.
- The server returned 404 Not Found in the captured response.
- This experiment did not capture traffic from another machine.
- It does not demonstrate interception across the internet.
- It did not perform a comparison against HTTPS.
- The results demonstrate only what was observable in this controlled plaintext HTTP capture.
