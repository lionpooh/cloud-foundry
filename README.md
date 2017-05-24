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
출처: [Loggregator](http://docs.cloudfoundry.org/loggregator/images/architecture.html)

[google](http://www.google.com)
