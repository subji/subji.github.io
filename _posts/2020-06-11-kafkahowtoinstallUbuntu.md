---
title: "Apache Kafka 설치"
date: 2020-06-11
categories: posts
tags: ["kafka"]
---

Apache Kafka 설치 방법

kafka 는 Scala 로 작성되어 있습니다. 따라서 자바가 설치되었는지 확인 후 다음을 진행해야됩니다.

kafka 클러스터 관리등의 이유로 zookeeper 가 선행되어야 합니다. 다행이도 zookeeper 는 kafka 설치파일에 포함되어 있습니다. 

나중에 실행할때도 zookeeper 를 먼저 실행하고 kafka 를 실행해줍니다.

### 1) Windows

[Kafka 공식 홈페이지](https://kafka.apache.org/downloads)에서 바이너리 파일을 받습니다.

압축 해제 후 사용자가 원하는 디렉토리로 이동시키고 bin 디렉토리 안에 kafka-server-start.bat 으로 실행합니다.

### 2) Linux (Ubuntu)

```sh
# Kafka 다운로드
wget https://www.apache.org/dist/kafka/2.5.0/kafka_2.13-2.5.0.tgz 

# 파일 압축 해제
tar zxf kafka_2.13-2.5.0.tgz

# kafka 디렉토리 이동 (선택사항)
mv kafka_2.13-2.5.0 /usr/local/kafka
```

리눅스의 경우 실행하기 쉽게 systemctl 명령어로 등록을 해줍니다.

```sh
# zookeeper 실행 명령어를 등록
vim /etc/systemd/system/zookeeper.service

[Unit]
Description=Apache Zookeeper server
Documentation=http://zookeeper.apache.org
Requires=network.target remote-fs.target
After=network.target remote-fs.target

[Service]
Type=simple
ExecStart=/usr/local/kafka/bin/zookeeper-server-start.sh /usr/local/kafka/config/zookeeper.properties
ExecStop=/usr/local/kafka/bin/zookeeper-server-stop.sh
Restart=on-abnormal

[Install]
WantedBy=multi-user.target

# kafka 명령어 등록
vim /etc/systemd/system/kafka.service

[Unit]
Description=Apache Kafka Server
Documentation=http://kafka.apache.org/documentation.html
Requires=zookeeper.service

[Service]
Type=simple
Environment="JAVA_HOME={java home directory}"
ExecStart=/usr/local/kafka/bin/kafka-server-start.sh /usr/local/kafka/config/server.properties
ExecStop=/usr/local/kafka/bin/kafka-server-stop.sh

[Install]
WantedBy=multi-user.target

# 새로 만든 zookeeper, kafka 명령어 등록
sudo systemctl daemon-reload

# zookeeper 실행
sudo systemctl start zookeeper

# kafka 실행
sudo systemctl start kafka
```

이렇게 설치 및 실행을 해보았습니다.