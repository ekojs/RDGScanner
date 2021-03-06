# Scanner for CVE-2020-0609 & CVE-2020-0610
A proof-of-concept scanner to check an RDP Gateway Server for vulnerabilities CVE-2020-0609 & CVE-2020-0610.

#### Tested on:
- Windows Server 2012 x64
- Windows Server 2016 x64
- Windows Server 2019 x64

## Disclaimer
Scanning for memory corruption vulnerabilities is dangerous and can potentially crash systems. 
Whilst this tool has been designed to avoid causing a crash, stability can't be 100% guaranteed.
**Use at your own risk and only scan systems you have permission to test.**

## Usage
``python3 RDGScanner.py <ip> <port>``

## How it works
To understand the vulnerabilities, you can read my writeup here:
https://www.kryptoslogic.com/blog/2020/01/rdp-to-rce-when-fragmentation-goes-wrong/

The CONNECT_PKT_FRAGMENT message allows a connect message to be fragmented across multiple packets. Each packet has a 
field specifying the position of the packet in the sequence (`fragment_id`) and the total number of packets in the 
sequence (`num_fragments`).

CVE-2020-0610 is an OOB write vulnerability which occurs when `fragment_id` is greater than 64. To mitigate the 
vulnerability, checks have been added to fail if either `fragment_id` or `num_packets` is greater than 64.

By setting `num_fragments` (but not `fragment_id`) to a value greater than 64, we fail the security check without 
risking exploiting the bug. On a patched system we will be disconnected with an error code of 0x8000ffff, whereas on an 
unpatched system nothing will happen.
