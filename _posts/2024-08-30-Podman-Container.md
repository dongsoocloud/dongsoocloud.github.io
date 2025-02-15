---
title: "[Container 컨테이너] Podman 설치 및 Nginx 컨테이너 실행, 중지 및 삭제 방법"
date: 2024-08-30 10:00:00 +0900
categories: [Container, Podman]
tags: [Container, Podman]
typora-root-url: ./
---

# **Podman 사용 방법**

---

### **Environment**

---

{: .prompt-info}

> Red Hat Enterprise Linux 8.4
>
> podman version 3.0.2-dev

<br/>

## **1. Podman 설치**

---

**`yum install -y podman`**

{: .prompt-tip}

> 다양한 컨테이너 관련 도구를 포함하는 모듈화된 패키지 설치는 다음 명령어로 실행할 수 있다.
>
> **`yum module install container-tools`**

<br/>

## **2. Docker Hub에서 Nginx 이미지 가져오기**

---

**`podman pull docker.io/nginx:latest`**

<br/>

{: .prompt-info}

> 기본적으로 이미지 저장 경로는 **/var/lib/containers/storage/overlay-containers**이고, 이 경로 설정은 **/etc/containers/storage.conf** 파일에서 할 수 있다.

<br/>

## **3. 컨테이너 생성 및 실행하기**

---

가져온 이미지를 사용하여 Nginx 컨테이너를 생성하고 실행한다.

<br/>

**`podman run -d --name mynginx -p 8080:80 docker.io/nginx:latest`**

```bash
1463ed694a92918be01d24381a235d41f1662ebe6967ed9cfad3959f67447608
```

<br/>

## **4. 실행 중인 컨테이너 목록보기**

---

**`podman ps`**

```bash
CONTAINER ID  IMAGE                   COMMAND               CREATED        STATUS            PORTS                 NAMES
1463ed694a92  docker.io/nginx:latest  nginx -g daemon o...  3 minutes ago  Up 3 minutes ago  0.0.0.0:8080->80/tcp  mynginx
```

**STATUS**가 **`Up`**이면 실행 중인 것이다.

<br/>

> 저장된 이미지 목록은 다음 명령어로 확인 가능하다.
>
> **`podman images`**

<br/>

## **5. 컨테이너 중지하기**

---

**`podman stop <container-id or container-name>`**

> podman stop 1463ed694a92

```bash
1463ed694a92918be01d24381a235d41f1662ebe6967ed9cfad3959f67447608
```

<br/>

전체 컨테이너 목록을 확인해보자.

**STATUS**가 **`Exited`** 된 것을 볼 수 있다.

<br/>

**`podman ps -a`**

```bash
CONTAINER ID  IMAGE                   COMMAND               CREATED        STATUS                    PORTS                 NAMES
1463ed694a92  docker.io/nginx:latest  nginx -g daemon o...  7 minutes ago  Exited (0) 3 seconds ago  0.0.0.0:8080->80/tcp  mynginx
```

<br/>

## **6. 중지된 컨테이너 다시 실행하기**

---

**`podman start <container-id or container-name>`**

> podman start 1463ed694a92

```bash
1463ed694a92
```

<br/>

전체 컨테이너 목록을 확인해보자.

**STATUS**가 **`Up`**으로 잘 올라온 것을 볼 수 있다.

<br/>

**`podman ps -a`**

```bash
CONTAINER ID  IMAGE                   COMMAND               CREATED         STATUS            PORTS                 NAMES
1463ed694a92  docker.io/nginx:latest  nginx -g daemon o...  10 minutes ago  Up 2 seconds ago  0.0.0.0:8080->80/tcp  mynginx
```

<br/>

## **7. 컨테이너 삭제하기**

---

**`podman rm <container-id or container-name>`**

> podman rm 1463ed694a92

<br/>

컨테이너 실행 중엔 다음과 같이 삭제가 되지 않는다.

**`-f`** 옵션을 통해 강제로 삭제하거나, 중지 후 삭제해야 한다.

```bash
Error: cannot remove container 1463ed694a92918be01d24381a235d41f1662ebe6967ed9cfad3959f67447608 as it is running - running or paused containers cannot be removed without force: container state improper
```

<br/>

**`podman rm -f mynginx`**

```bash
1463ed694a92918be01d24381a235d41f1662ebe6967ed9cfad3959f67447608
```

<br/>

전체 컨테이너 목록을 확인해보면 컨테이너가 제거된 것을 확인할 수 있다.

<br/>

**`podman ps -a`**

```bash
CONTAINER ID  IMAGE   COMMAND  CREATED  STATUS  PORTS   NAMES
```

<br/>

## **References**

---

<https://podman.io/docs/installation>

<https://access.redhat.com/solutions/3650231>

<br/>
