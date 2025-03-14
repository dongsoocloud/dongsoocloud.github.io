---
title: "[GCP] Troubleshooting Websocket App Deployed on the Serverless Environment."
date: 2025-03-13 16:10:00 +0900
categories: [Google Cloud Platform, Cloud Run]
tags: [Google Cloud Platform, Cloud Run, tcpdump]
---

<script async src="https://pagead2.googlesyndication.com/pagead/js/adsbygoogle.js?client=ca-pub-2941907865454687"
     crossorigin="anonymous"></script>

Due to their unique characteristics, serverless services can sometimes exhibit unexpected behavior that wasn’t captured in local test environments. This is especially true when testing a WebSocket application on a serverless platform, where you may encounter various unforeseen issues. These issues can stem from different sources, including the application layer, network layer, or underlying infrastructure of cloud provider's side.

In this post, I will explain how to troubleshoot network issues in a WebSocket application deployed on a serverless computing service. The example will be based on Google Cloud’s serverless platform, Cloud Run[1].

## Step 1/3. Deploy the sample websocket application

If you don't have a sample websocket app on your end, don't worry. We are going to use this sample websocket container from the public docker hub.
https://hub.docker.com/r/ksdn117/web-socket-test

Here's a Cloud Run Settings.

- Conatiner Image URL: ksdn117/web-socket-test
- Authentication: Allow unauthenticated invocations
- Container port: 8010
  ![cloudrun_setting.jpg](/../assets/img/posts/2025-03-13-gcp-websocket-test/cloudrun_setting.jpg)

Once the build is done, you can see your service on the Cloud Run service dashboard like below.
![cloudrun_deployed.jpg](/../assets/img/posts/2025-03-13-gcp-websocket-test/cloudrun_deployed.jpg)
Click your service and memo the given URL you can see on the detail page.  
Mine is `https://websocket-app-272932108507.asia-northeast1.run.app`
![cloudrun_detail.jpg](/../assets/img/posts/2025-03-13-gcp-websocket-test/cloudrun_detail.jpg)
Now let's test the app. To test, you need to have a websocket client tool `wscat`. Please download and install it on your environment[2].

```bash
npm install -g wscat
```

Let's run the following command to establish websocket connection from your client to the Cloud Run service.

```bash
wscat --connect https://your-cloudrun-url.asia-northeast1.run.app
```

Now you can see the application work successfully.
![wscat_test.jpg](/../assets/img/posts/2025-03-13-gcp-websocket-test/wscat_test.jpg)

## Step 2/3. Check the IP addresses of the service.

We've used the given domain name, but what IP addresses does it resolve to?
To check this, you can use `dig` or `nslookup`.  
We will use `dig` and send the DNS query to the Google's public name server.

```bash
dig @ns1.google.com https://your-cloudrun-url.asia-northeast1.run.app
nslookup https://your-cloudrun-url.asia-northeast1.run.app ns1.google.com
```

![dig_result.jpg](/../assets/img/posts/2025-03-13-gcp-websocket-test/dig_result.jpg)

As you can see from the above, the given domain name is resolved to 4 different IPv4 addresses. Any Cloud Run service domain will be resolved to one of those 4 IP addresses as its domain \*.run.app is anycast.

```
216.239.38.53
216.239.32.53
216.239.34.53
216.239.36.53
```

You can also run with `dig AAAA` to query IPv6 addresses.

## Step 3/3. Monitor packets routed to each IP address.

To troubleshoot connectivity issues with the Cloud Run service, we need to monitor network packets.
However, the destination IP address changes each time you establish a WebSocket connection, cycling among the four IP addresses listed above. In this example we will use IP 216.239.32.53.
You can lock the IP address by adding an entry `216.239.32.53   your-cloudrun-url.asia-northeast1.run.app` to your local /etc/hosts file

Now let's prepare another terminal (terminal B) to monitor the real-time packet routes. On the terminal B, let's run `tcpdump`.

```bash
sudo tcpdump host 216.239.36.53 -n
```

At the same time, on the original terminal (terminal A), run the `wscat --connect` to connect to the Websocket service.

```bash
wscat --connect https://your-cloudrun-url.asia-northeast1.run.app
```

Now you can monitor the packets going back and forth between your working environment and the Websocket application.
![tcpdump_test.jpg](/../assets/img/posts/2025-03-13-gcp-websocket-test/tcpdump_test.jpg)

[1] https://cloud.google.com/run/docs/overview/what-is-cloud-run  
[2] https://www.npmjs.com/package/wscat
