# cloud-foundry

## Loggregator

Loggregator�� CF�� user app��� System Component���� log�� metric�� ������ �����ϱ� ���� �ý����̴�.

* App �����ڴ� application�� log�� ���� �ֱ� log�� Cloud Foundry�� CLI(cf Command LIne Interface) Ȥ��, third-party log ����ҿ� �м� ���񽺸� ���ؼ�
�� �� �ִ�.
* �����ڿ�, ���۷����ʹ� Loggregator�� Firehose�� ���ؼ� ���� �� �� ������, ���⼭ ��� app���� log�� cloud foudnry component���� metric�������� ������.

* ���۷����ʹ� Firehose�� Nozzle�� ����� �� ������, Nozzle�� Firehose�� ����͸� �ؼ� Ư�� �̺�Ʈ�� metric�� ã�Ƽ� �ܺ� ���񽺷� �������ش�. 

<p align="center">
<img src="http://docs.cloudfoundry.org/loggregator/images/architecture/loggregator.png" width="90%">
</p>
��ó - [Loggregator](http://docs.cloudfoundry.org/loggregator/images/architecture/loggregator.png)