---
title: "[GCP] Troubleshooting Websocket App Deployed on the Serverless Environment."
date: 2025-03-13 16:10:00 +0900
categories: [Google Cloud Platform, Cloud Run]
tags: [Google Cloud Platform, Cloud Run, tcpdump]
---

Due to their unique characteristics, serverless services can sometimes exhibit unexpected behavior that wasn’t captured in local test environments. This is especially true when testing a WebSocket application on a serverless platform, where you may encounter various unforeseen issues. These issues can stem from different sources, including the application layer, network layer, or underlying infrastructure of cloud provider's side.

In this post, I will explain how to troubleshoot network issues in a WebSocket application deployed on a serverless computing service. The example will be based on Google Cloud’s serverless platform, Cloud Run[1].

### Step 1. Deploy the sample websocket application

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

Let's run the following command to establish websocket connection from your client to the Cloud Run service.

```bash
wscat --connect https://your-cloudrun-url.asia-northeast1.run.app:8010
```

[1] https://cloud.google.com/run/docs/overview/what-is-cloud-run  
[2] https://www.npmjs.com/package/wscat

0, 동기:
웹소켓 관련 이슈가 많아서 빠르게 웹소켓을 Cloud Run에 배포하는 방법을 확인해본 후,
1, Websocket 을 테스트 해보는 방법
샘플 ksdn117/web-socket-test
2, 클라이언트에서 테스트
wscat --connect https://tagtesta.run.app
3, 자동으로 부여된 도메인 네임이 있다. 실제로는 어떤 IP로 Resolve될까?
nslookup
dig
4, 서버리스 환경. 패킷이 분석을 해보았다. 어떤 케이스는 특정 IP가 제대로 응답하지 않는 경우도 있기 때문.위의 IP중에서 특정 IP로 보내고, 해당 IP에서 응답이 잘 오는지 확인

- 먼저 분석하고자 하는 IP주소로 가도록.
  tcpdump -n host 312312124
