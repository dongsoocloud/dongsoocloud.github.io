---
title: "[GCP] Troubleshooting Websocket App Deployed on the Serverless Environment."
date: 2025-03-13 16:10:00 +0900
categories: [Google Cloud Platform, Cloud Run]
tags: [Google Cloud Platform, Cloud Run, tcpdump]
---

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

We've used the given domain name. However, to what IP addresses would it be resolved?
To check this, you can use `dig` or `nslookup`.  
We used `dig` and send the DNS query to the Google's public name server.
You can also add AAAA to query IPv6 addresses.

```bash
dig @ns1.google.com https://your-cloudrun-url.asia-northeast1.run.app
nslookup https://your-cloudrun-url.asia-northeast1.run.app ns1.google.com
```

![dig_result.jpg](/../assets/img/posts/2025-03-13-gcp-websocket-test/dig_result.jpg)

As you can see from the above, the given domain name is resolved to 4 different IPv4 addresses.

## Step 3/3. Monitor packets routed to each IP address.

만약 특정 IP로의 Connectivity issue가 생겼을 경우 등을 위해 특정 IP주소를 지정해서 패킷을 모니터링 해보려고 한다.
먼저 패킷이 특정 IP로만 갈 수 있도록 /etc/hosts 파일을 다음과 같이 세팅해준다
(사진)

그리고 터미널을 하나 더 추가(터미널 B)해서 `tcpdump`로 모니터링해본다.
`sudo tcpdump host 216.239.36.53 -n`

그리고 기존 터미널 A에서는 다시 wscat을 이용해 웹소켓 연결을 해보자.

다음과 같은 결과를 확인할 수 있다.
(사진 두개 터미널 함께 보여줌)

[1] https://cloud.google.com/run/docs/overview/what-is-cloud-run  
[2] https://www.npmjs.com/package/wscat
