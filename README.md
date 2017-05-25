
<img src="https://cdn.pixabay.com/photo/2013/07/12/13/58/warning-147699_960_720.png" width="30%"/>

**주의!** 이 문서는 내 맘대로 번역한 문서입니다. 
* 태클을 걸고 싶거나 문의 내용이 있으시다면 lionpooh37@gmail.com으로 문의를 주시면 '아마도' 읽을 것입니다.
* 저작권에 위배되는 내용이라면 언제든지 삭제 및 수정이 될 수 있습니다.

# Cloud-Foundry

<img src="https://upload.wikimedia.org/wikipedia/en/thumb/b/bb/CloudFoundryCorp_vertical.svg/450px-CloudFoundryCorp_vertical.svg.png"/>

**Cloud Foundry**란? 
>**OpenSource PaaS Platform**

* Cloud Foundry에 관련된 것들을 끄적인 문서, ~~당신이 원하는 내용이 없을 수도 있다~~
* 나머지 것들은 언젠가 정리하게 될 것이다. ~~하지 않을 것이다~~
* 자료 원본 링크 - http://docs.cloudfoundry.org/ ~~여기 어딘가에서 퍼온 것들~~
* 사용을 해본적도 없고 구축도 해본적이 없다. 따라서 그냥 문서내용을 기준으로 번역만 한 것이다.
	* PCF(Pivotal Cloud Foundry라는 것이 있는 데 회원가입을 하고 제한적이지만 CF기반의 PaaS를 써볼 수 있다.) - [링크](https://pivotal.io/platform)

## Loggregator

Loggregator는 CF의 user app들과 System Component들의 log와 metric을 모으고 전달하기 위한 시스템이다.

* App 개발자는 application의 log나 가장 최근 log를 Cloud Foundry의 CLI(cf Command LIne Interface) 혹은, third-party log 저장소와 분석 서비스를 통해서
볼 수 있다.
* 관리자와, 오퍼레이터는 Loggregator를 Firehose를 통해서 접근 할 수 있으며, 여기서 모든 app들의 log와 cloud foudnry component들의 metric정보들을 모은다.

* 오퍼레이터는 Firehose에 Nozzle을 사용할 수 있으며, Nozzle은 Firehose를 모니터를 해서 특정 이벤트와 metric을 찾아서 외부 서비스로 전달해준다.

<p align="center">
<img src="http://docs.cloudfoundry.org/loggregator/images/architecture/loggregator.png" width="90%">
</p>

[이미지 출처](http://docs.cloudfoundry.org/loggregator/architecture.html)

>**Note** - Loggregator 시스템은 안정성과, 성능 향상을 위해서  Metron Agent와 Doppler, Doppler와 Traffic Controller는 gRPC를 통해서 서로 통신을 한다. 하지만 이러한 점 때문에 Operator는 Doppler의 scale을 고려해야 할지도 모른다. (그림 처럼 Doppler를 여러개를 두어서 load balance한다는 의미인듯?)

### Source
Source는 logging agent이며 Cloud Foundry Component위에서 동작한다.


### Metron
Metron agent는 Source와 같은 곳에 있으며, log를 수집해서 Doppler server에게 전달해 준다.


### Doppler
Doppler는 Metron agent로 부터 log를 수집해서 임시 버퍼에 저장하고 있다가, Traffic Controller혹은, third-party syslog drain에게 전달해준다.
* third-party?


### Trafic Controller
Traffic Controller는 Doppler server로 부터 오는 메시지들을 처리하며, 모으고 수집하여서 Firehose쪽으로 보내줍니다. 또한 external API와 메시지 변환을 위한 Lagacy API들을 제공합니다.


### Firehose
Firehose는 WebSocket endpoint이며, Cloud Foundry의 모든 event data들이 모이게 된다. 이러한 데이터는 Log를 포함하고 있으며, HTTP event들 뿐만 아니라 container의 모든 application metric(사용자가 올린 app이 아닌듯 하다)과 Cloud Foundry의 모든 system component들의 metric을 포함하고 있다. system component들의 Log들은 (e.g Cloud Controller) firehose로 넘어오지 않는다. 일반적으로 rsyslog configuration을 통해서 접근하게 된다.

Firehose를 통해서 넘어오는 데이터 중에는 민감한 데이터가 포함되어 있을 수가 있다. 예를 들어 고객의 정보가 담긴 정보같은 경우에는  그 권한에 맞는 사용자만 Firehose에 접근할수 있게 해야 한다.


### Diffrence Between Logs and Metrics
Firehose는 log와 metric정보들을 가지고 다닌다. 그 둘의 차이점은 다음과 같다.
* Metrics
	* VM의 measurement, status와 체크한 때의 시간(timestamp)을 포함한다.
	* dropsonde 혹은 statsd protocol을 따른다.
	* monitoring platform으로 trigger alerts을 전달할수 있다.
* Logs
	* event 감시, action에 대한 결과, errors, 혹은 operator나 개발자가 커스텀 해서 추가한 메시지를 포함할수 있다.
	* syslog standard를 따른다.
	* trigger alert은 사용하지 않는다.


### Nozzles
Nozzle들은 Loggregator Firehose에서 부터 데이터를 consume(수집이라고 보면 될듯)하기 위한 용도로 사용된다. Nozzle은 데이터를 선택하고, 버퍼에 넣고, 변환하여 다른 application혹은 service에게 전달해줄수 있다.

Nozzle example
* The JMX Bridge OpenTSDB Firehose Nozzle, JMX Bridge와 같이 설치가 된다.
* The Datadog nozzle, Firehose에서 Datadog으로 metric들을 전달해준다.
* The Syslog nozzle, Firehose에서 filter 처리가 되서 나온 log 메시지들을 syslog server에게 보내준다. 