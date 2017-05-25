
<img src="https://cdn.pixabay.com/photo/2013/07/12/13/58/warning-147699_960_720.png" width="30%"/>

* 이 문서는 내 맘대로 번역한 문서입니다. 태클을 걸고 싶거나 문의 내용이 있으시다면 lionpooh37@gmail.com으로 문의를 주시면 '아마도' 무엇인가 할 것입니다.[^1]
* 저작권에 위배되는 내용이라면 언제든지 삭제 및 수정이 될 수 있습니다.

# cloud-foundry

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

Note - Loggregator 시스템은 안정성과, 성능 향상을 위해서  Metron Agent와 Doppler, Doppler와 Traffic Controller는 gRPC를 통해서 서로 통신을 한다. 하지만 이러한 점 때문에 Operator는 Doppler의 scale을 고려해야 할지도 모른다. (그림 처럼 Doppler를 여러개를 두어서 load balance한다는 의미인듯?)

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
Firehose는 WebSocket endpoint이며, Cloud Foundry의 모든 event data들이 모이게 된다. 이러한 데이터는 Log를 포함하고 있으며, HTTP event들 뿐만 아니라 container의 모든 application metric과  

---

[^1]: 응안해
