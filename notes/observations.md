# Packet observations

  - Capture date/time (timezone): September 24, 2026, EDT
  - Environment: Ubuntu Desktop VM; Wireshark 4.6.4; Python 3.14.4
  - Capture interfact: lo; capture filter: tcp port 8000; destination: 127.0.0.1:8000
  - TCP stream number: 0
  - Handshake: SYN frame 2, SYN+ACK frame 3, ACK frame 4
  - HTTP request: GET frame 5; target '/marker=DEMO_ONLY_123'
  - HTTP response: frame 9; status '404 not found'
  - Interpretation: the HTTP request was readable in plaintext in the local loopback capture. The fictional marker 'DEMO_ONLY_123' was visible in the decoded HTTP request and in the followed TCP stream.
  - Limitations: this capture was generated entirely on the Ubuntu VM loopback interface, used a synthetic marker, and did not compare HTTP w/ HTTPS
