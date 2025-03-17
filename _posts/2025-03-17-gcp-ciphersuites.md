---
title: "[GCP] Monitoring and Configuring Cipher Suites on the Server"
date: 2025-03-17 11:10:00 +0900
categories: [Google Cloud Platform, Network]
tags: [Google Cloud Platform, Cloud Run, network, security]
---

When deploying a service on Google Cloud or calling an external API, ensuring security is crucial. In particular, when undergoing a security compliance audit, it's important to know which TLS versions and cipher suites your service or an external API is using.

In this post, I'll address three common questions I often encounter in the workplace:

1. How can I check if my server or an external API is using secure cipher suites?

2. Why do some external APIs support both older and newer TLS versions?

3. Can I configure the cipher suites used by my Cloud Run or App Engine server?

## Q1. How can I check if my server or an external API is using secure cipher suites?

To check the supported TLS versions and cipher suites of a server, you can use the nmap command. nmap is an open-source utility for network discovery and security auditing, and it includes useful scripts like ssl-enum-ciphers for checking SSL/TLS configurations.

Run the following command to check the cipher suites of a server:  
`$ nmap --script ssl-enum-ciphers -p 443 SERVER_HOST`

For example, to inspect the Google Cloud Storage API (storage.googleapis.com), use:  
`$ nmap --script ssl-enum-ciphers -p 443 storage.googleapis.com`  
This command will return a list of supported TLS versions and cipher suites for the API server like below.

```shell
...
PORT    STATE SERVICE
443/tcp open  https
| ssl-enum-ciphers:
|   TLSv1.0:
|     ciphers:
|       TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA (ecdh_x25519) - A
|       TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA (ecdh_x25519) - A
|       TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA (ecdh_x25519) - A
|       TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA (ecdh_x25519) - A
|       TLS_RSA_WITH_AES_128_CBC_SHA (rsa 2048) - A
|       TLS_RSA_WITH_AES_256_CBC_SHA (rsa 2048) - A
|       TLS_RSA_WITH_3DES_EDE_CBC_SHA (rsa 2048) - C
|     compressors:
|       NULL
|     cipher preference: server
|     warnings:
|       64-bit block cipher 3DES vulnerable to SWEET32 attack
|   TLSv1.1:
|     ciphers:
|       TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA (ecdh_x25519) - A
|       TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA (ecdh_x25519) - A
|       TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA (ecdh_x25519) - A
|       TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA (ecdh_x25519) - A
|       TLS_RSA_WITH_AES_128_CBC_SHA (rsa 2048) - A
|       TLS_RSA_WITH_AES_256_CBC_SHA (rsa 2048) - A
|       TLS_RSA_WITH_3DES_EDE_CBC_SHA (rsa 2048) - C
|     compressors:
|       NULL
|     cipher preference: server
|     warnings:
|       64-bit block cipher 3DES vulnerable to SWEET32 attack
|   TLSv1.2:
|     ciphers:
|       TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 (ecdh_x25519) - A
|       TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 (ecdh_x25519) - A
|       TLS_ECDHE_ECDSA_WITH_CHACHA20_POLY1305_SHA256 (ecdh_x25519) - A
|       TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA (ecdh_x25519) - A
|       TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA (ecdh_x25519) - A
|       TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 (ecdh_x25519) - A
|       TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 (ecdh_x25519) - A
|       TLS_ECDHE_RSA_WITH_CHACHA20_POLY1305_SHA256 (ecdh_x25519) - A
|       TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA (ecdh_x25519) - A
|       TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA (ecdh_x25519) - A
|       TLS_RSA_WITH_AES_128_GCM_SHA256 (rsa 2048) - A
|       TLS_RSA_WITH_AES_256_GCM_SHA384 (rsa 2048) - A
|       TLS_RSA_WITH_AES_128_CBC_SHA (rsa 2048) - A
|       TLS_RSA_WITH_AES_256_CBC_SHA (rsa 2048) - A
|       TLS_RSA_WITH_3DES_EDE_CBC_SHA (rsa 2048) - C
|     compressors:
|       NULL
|     cipher preference: server
|     warnings:
|       64-bit block cipher 3DES vulnerable to SWEET32 attack
|   TLSv1.3:
|     ciphers:
|       TLS_AKE_WITH_AES_256_GCM_SHA384 (ecdh_x25519) - A
|       TLS_AKE_WITH_AES_128_GCM_SHA256 (ecdh_x25519) - A
|       TLS_AKE_WITH_C
```


## Q2. Why do some external APIs support both older and newer TLS versions?

As seen in the output from nmap, many APIs support multiple TLS versions, including the latest TLS 1.3 as well as older versions like TLS 1.0, 1.1, and 1.2. This may raise concerns about security compliance, especially if an external API allows connections using older, weaker TLS versions.

However, if an API supports a wide range of TLS versions, it doesn't necessarily mean it is insecure. Many API providers enable weaker cipher suites to maintain compatibility with older devices. In practice, modern clients should automatically negotiate the strongest available cipher suite.

During the TLS handshake, the client proposes its highest supported TLS version, and the server selects the best mutually supported version. If your client supports TLS 1.3, it should default to using it instead of an older version.

## Q3. Can I configure the cipher suites used by my Cloud Run or App Engine server?

Yes, you can configure cipher suites for your Cloud Run or App Engine service by placing a Google Cloud Load Balancer in front of it and applying an SSL policy.

Steps to configure an SSL policy:

1. Set up a Google Cloud Load Balancer in front of your Cloud Run or App Engine service.

2. Create an SSL policy specifying the allowed TLS versions and cipher suites.

3. Attach the SSL policy to the Load Balancer.

By doing this, you can enforce stricter security policies and ensure your service only supports the TLS versions and cipher suites that meet your security requirements.

--

<script
  src="https://utteranc.es/client.js"
  repo="dongsoocloud/blog-comments"
  issue-term="title"
  theme="github-light"
  crossorigin="anonymous"
  async
></script>
