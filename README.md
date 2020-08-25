# Sysmon-EL-PyQt
Sysmon logs in the window environment are received from a computer in another environment through winlogbeat through Logstash, and then repositioned in Elasticsearch and displayed in PyQt.

## [HOST] Winodw10 Enviroment ( Install Sysmon & Winlogbeat )

### Sysmon


### Winlogbeat


## [SERVER] Linux Enviroment ( Install Elasticsearch & Logstash by Docker )


### Elasticsearch 


### Logstash

## [SERVER] PyQt5 by Python 

### Archietecture

### Layout

### Function

#### Abstract

#### Abnormal behavior

#### Special List

#### Make WhiteList

#### Search BlackList


</br>

<details>
<summary>Timeline</summary>


<div markdown="1">

## 2020.08.18 
 local -> git connected
## 2020.08.19
 whitelist 함수 정의 및 구현
## 2020.08.20
* Upload Reference </br>
* server에서 client의 값이 들어오지 않을때</br> 
 1. network 상태가 이전 상태와 같은지 확인
 2. network 상태가 같다면 ip의 변동이 있는지 확인
 3. network 상태가 바뀌었다면 winlogbeat.yml의 ip를 확인
* Upload ui file
## 2020.08.21
* Update Whitelist 이외의 layout 구현 완료
* Error 찾는중 1번 실행은 잘 되지만 clear이후 2번실행은 값이 변하지 않는 것이 발견됌
## 2020.08.22
* Abnormal Tab idea구상
* Abnormal Tab layout 구현 완료
## 2020.08.24
* Abnormal .hwp file open 잡기 가능
## 2020.08.25
* Sysmon - EL - PyQt5 구현 완료
 

</div>
</details>

<details>
<summary>Reference(CLICK)</summary>
 

<div markdown="1">

### docker ELK와 sysmon 연동


### 1. 개요


#### 1.1 아키텍처

1. 수집한 sysmon이벤트를 winlogbeat를 사용하여 ELK서버의 logstash로 전달
2. logstash는 sysmon이벤트를 elasticsearch에 전달
3. kibana로 elasticsearch의 데이터를 탐색


### 2. 서버설정
* 서버 버전: ubuntu 18.04LTS </br>
* docker, docker-compose 설치



#### 2.1 고정IP설정
- ubuntu18부터는 이전 ubunut와 다르게 IP설정이 변경됨 </br>
    

#### 2.2 도커 이미지 다운로드
```
$ git clone https://github.com/deviantony/docker-elk.git
$ sudo docker-compose build
```


#### 2.3 elasticsearch 설정
* X-Pack설정 일부 비활성화 <br/>

```
$ vi ./elasticsearch/config/elasticsearch.yml

cluster.name: "docker-cluster" 
network.host: 0.0.0.0 
discovery.type: single-node
```


#### 2.4 kibana 설정
```
$ vi ./kibana/config/kibana.yml

server.name: kibana
server.host: "0"
elasticsearch.hosts: [ "http://elasticsearch:9200" ]
xpack.monitoring.ui.container.elasticsearch.enabled: true
```


#### 2.5 logstash설정
```
$ vi ./logstash/config/logstash.yml

http.host: "0.0.0.0"
xpack.monitoring.elasticsearch.hosts: [ "http://elasticsearch:9200" ]
```

```
$ vi ./logstash/pipeline/logstash.conf

input {
    beats{
        port => 5000
    }    
}

output {
    elasticsearch {
        hosts => "elasticsearch:9200"
        user => "usernmaet"
        password => "password"
        index => "%{[@metadata][beat]}-%{+YYYY.MM.dd}"
        document_type => "%{[@metadata][type]}"
    }
}
```

#### 2.6 도커 컨테이너 실행
- d인자는 데몬실행(각 컨테이너가 백그라운드로 서비스 실행)
```
# sudo docker-compose up -d
```

> 컨테이너 기본포트
5000: Logstash TCP input
9200: Elasticsearch HTTP
9300: Elasticsearch TCP transport
5601: Kibana


#### 2.7 도커 컨테이너 종료
- 모든 도커 컨테이너 종료
```
# sudo docker-compose down
```


### 3. 악성코드 실행하는 가상머신 설정

#### 3.1 winlogbeat 설치&설정
- 다운로드:  [https://www.elastic.co/kr/downloads/beats/winlogbeat](https://www.elastic.co/kr/downloads/beats/winlogbeat)
![docker%20ELK%20sysmon/Untitled%201.png](docker%20ELK%20sysmon/Untitled%201.png)


- sysmon데이터 전송을 위한 winlogbeat.yml설정
```
winlogbeat.event_logs:
    - name: Microsoft-Windows-Sysmon/Operational

output.logstash:
    # The Logstash hosts
    hosts: ["우분투IP:5000"]
    index: winlogbeat
```    

#### 3.2 winlogbeat 설정 적용
- winlogbeat 설정파일 적용
```
PS> .\winlogbeat.exe -c .\winlogbeat.yml
```


#### 3.3 sysmon 실행
```
PS> .\sysmon.exe -i [sysmon설정파일.xml]
PS> .\sysmon.exe -c [sysmon 업데이트 파일.
```
* 설정파일이 없으면 https://github.com/SwiftOnSecurity/sysmon-config/blob/master/sysmonconfig-export.xml 다운받아 사용


#### 3.4 winglobeat 실행
```
PS> .\install-service-winlogbeat.ps1
PS> start-service winlogbeat
```

### 4. 참고자료
* docker Sysmon-ELK: 
 https://github.com/choisungwook/malware/tree/master/01%20blue%20team/sysmon/01%20elk%EC%84%A4%EC%B9%98%2B%EC%97%B0%EB%8F%99
* Docker, ELK: https://judo0179.tistory.com/60
* Docker, ELK: https://github.com/deviantony/docker-elk
* winlogbeat: https://cyberwardog.blogspot.com/2017/02/setting-up-pentesting-i-mean-threat_87.html
* ubuntu18 고정 IP 설정: https://www.manualfactory.net/10455

</div>
</details>
