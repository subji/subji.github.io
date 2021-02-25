---
title: "Eureka self-preservation"
date: 2020-08-11
layout: single
classes: wide
categories: posts
tags: ["msa", "eureka"]
---

### **Eureka**
---

Eureka 는 [CAP 정리](https://ko.wikipedia.org/wiki/CAP_%EC%A0%95%EB%A6%AC) 측면에서 볼 수 있는 AP 시스템이다. 여러개의 네트워크 파티션 있다고 했을때 각각의 파티션들의 레지스트리 값은 일치하지 않게 된다. Eureka 의 *Self-preservation* 기능은 이러한 불일치를 최소화 해주는 특징이다.

<br>

## **Eureka Self-Preservation ?**
---

Self Preservation 은 Eureka 서버에서 특정 시간이나 횟수등의 값을 초과하는 heartbeat 를 수신하지 못할때 Eureka 레지스트리에서 해당 인스턴스의 만료를 중지하는 기능이다.

heartbeat 는 각각의 Eureka 클라이언트로 어떠한 상태값이나 특정 값을 받아 해당 클라이언트의 상태를 알 수 있는 정보이다.

<br>

## **Eureka Self-Preservation Flow**
---

- Eureka Server 1, Server 2 가 존재
- Eureka Client Intance 1, 2, 3, 4, 5 => Eureka Server 1 에 등록
- Eureka Server 1 -> Server 2 로 등록 복사
- 각각의 서버 레지스트리에 Instance 1, 2, 3, 4, 5 가 존재
- Eureka Client Instance 2 는 Instance 4 와 연결 중이다.
- Eureka Client Instance 4, 5 가 네트워크 파티션으로 분리
- Eureka Server 는 Instance 4, 5 의 Heartbeat 가 더이상 수신되지 않는것을 확인하고 레지스트리에서 Instance 4, 5 제거
- Eureka Server 에서 갑자기 15% 이상의 Instance 가 종료됨을 알고 Self-Preservation 모드 실행
- 이제부터 Eureka Server 는 나머지 1, 2, 3 Instance 가 종료되더라도 레지스트리에서 만료될 경우에만 Instance 를 중지
- 즉, Instance 3 을 종료시키더라도 서버 레지스트리에는 활성 상태로 남아있다. 또한 Instance 3 자리에 새로운 Instance 등록을 허용한다.

<br>

## **Why Self-Preservation ?**
---

Self-Preservation 모드의 활성화 이유?

- 클라이언트의 종료가 아닌 잘못된 네트워크 파티션 분리로 인한 heartbeat 가 수신이 안되는 현상일 수 있다. 이 경우 잘못된 네트워크 파티션을 수정할 경우 원래대로 등록이 가능하므로 무조건적으로 레지스트리에서 제거하는 것은 옳지 않을 수 있다.
- 일부 Instance 의 연결이 끊어지더라도 또 다른 Instance 와 연결이 있을 경우 강제로 레지스트리에서 제거할 때 오류가 발생할 수 있다. 따라서 특정 시간 또는 횟수 동안 레지스트리에 활성 상태로 두는 것이 낫다.

<br>

## **About Self-Preservation configuration**
---

**eureka.instance.lease-renewal-interval-in-seconds = 30**
: Eureka Client 가 여전히 활성 상태임을 나타내기 위해 Eureka Server 에 heartbeat 를 보내는 빈도를 나타내며 기본값은 30초이다. 즉, 30초 마다 한번씩 heartbeat 를 수신하게 된다. 가급적 기본값인 30초 사용을 권장한다.

**eureka.instance.lease-expiration-duration-in-seconds = 90**
: Server 레지스트리에서 Client Instance 를 제거하기 전에 마지막 heartbeat 를 받은 후 서버가 대기하는 시간을 나타낸다.   
이값은 앞서 말한 *lease-renewal-interval-in-seconds* 보다 커야한다. 이 값을 너무 길게 설정하면 레지스트리의 활성 상태가 설정값에 따라 달라지므로 이전에 설명한 *lease-renewal-interval-in-seconds* 의 분당 실제 heartbeat 계산의 정밀도에 영향을 주게 된다고 한다. 또한 너무 작게 설정하면 시스템이 일시적인 네트워크 결함을 견딜 수 없게 된다고 한다.

**eureka.server.eviction-interval-timer-in-ms = 60 * 1000**
: 이 설정은 Instance 가 종료되고 만료가 되었을때 레지스트리에서 Instance 를 제거하는 스케쥴러가 이 빈도로 실행된다. lease-expiration-duration-in-seconds 의 값을 너무 길게 설정하면 시스템이 자체 Self-Preservation 으로 들어가는 것이 지연된다.

**eureka.server.renewal-percent-threshold = 0.85**
: 이 값은 분당 예상 heartbeat 를 계산하는 데 사용된다.    
이값의 계산은 Eureka 대시보드를 보면 알 수 있는데, Renews Threshold 와 Renews (last min) 부분을 보면 알 수 있다. 첫번째는 갱신 기준이 되는 임계값이며, 두번째는 마지막 갱신된 값이다. 즉 "EMERGENCY! EUREKA MAY BE INCORRECTLY CLAIMING INSTANCES ARE UP WHEN THEY'RE NOT. RENEWALS ARE LESSER THAN THRESHOLD AND HENCE THE INSTANCES ARE NOT BEING EXPIRED JUST TO BE SAFE." 와 같은 에러가 나면서 Instance 가 종료되지 않는다면 **Renews Threshold** 값이 **Renews (last min)** 보다 클 경우에 발생하는 에러이다. 임계 값이 더 클 경우 Self-Preservation 모드가 실행되며 만료된 Instance 가 종료되지 않기 때문이다.   
계산 방법은 다음과 같다.

- Renews Threshold => 분당 heartbeat 수 (기본값인 30초에 한번으로 가정) * Instance 수 + Spring 에서 구성하는 최소 Instance 수 (1) * renewal-percent-threshold 값 계산 후 반올림을 한다.
  - ex: 2 * 1 + 1 * 0.85 = 3
- Renews (last min) => 분당 heartbeat 수 (기본값인 30초에 한번으로 가정) * Instance 수 
  - ex: 2 * 1 = 2

즉, 테스트 환경이나 별도의 운영환경 구성에서 앞서말한 경고를 없애고자 할때 퍼센트 값을 낮추면 되겠다.

**eureka.server.renewal-threshold-update-interval-ms = 15 * 60 * 1000**
: 레지스트리에서 Instance 를 제거하는 스케줄러는 분당 예상 heartbaet 를 계산하는 것이 여기서 설정된 빈도 값 만큼 실행된다.

**eureka.server.enable-self-preservation = true**
: Self-Preservation 모드를 켜고 끌 수 있는 설정이다. 가급적이면 운영 상황에서는 끄지 않는 것을 권장한다. 

<br>

## **참고**
---

[The Mystery of Eureka Self-Preservation](https://dzone.com/articles/the-mystery-of-eurekas-self-preservation)