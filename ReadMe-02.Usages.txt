[[ START ]]

* Win7sp1 sysmon vm ȯ�� ����

* Win7sp1 sysmon vm ȯ�濡�� sysmon ���� ����(������ ����)
  > sysmon.exe -i %configfile%
    (������ ��ġ�ߴٸ� �ʿ� ����)

* Win7sp1 sysmon vm ȯ�濡�� winlogbeat ����(������ ����)
  > winlogbeat.exe -c winlogbeat.yml

* Elastic Kibana ����(������ ����)
  > bin/kibana.bat

* Elasticsearch ����(������ ����)
  > bin/elasticsearch.bat

* Ubuntu 18.04 64bit ȯ�濡�� Elasticalert ����
  >/elastalert  
  >elastalert --verbose --start  --config <config.yaml> --rule <error.yaml>


[[ �޴��� ]]

* sysmon
  > https://github.com/trustedsec/SysmonCommunityGuide/blob/master/Sysmon.md

* elastic
  > https://www.elastic.co/guide/en/elastic-stack-get-started/7.6/get-started-elastic-stack.html#install-elasticsearch

* elastalert
  > https://elastalert.readthedocs.io/en/latest/running_elastalert.html
