# Intelligent Incident Response Platform
## Index
- [Diagram](#Diagram)
- [Preference](#Preference)
- [How to install](#How-to-install)
- [How to run](#How-to-run)
- [Manual](#Manual)
- [Trouble shooting](#Trouble-shooting) 


##  [Diagram](#index)

   ![diagram](https://user-images.githubusercontent.com/63506170/130434242-fe60e942-c347-4ba2-afe9-c08100ff480b.jpg)

##  [Preference](#index)
 * HOST

| Name | Version | Remark |
|:-------|:-------|:-------:|
| Elasticsearch | 7.11.2 | - |
| kibana | 7.11.2 | - |
| Slack | - | - |

 * Guest 1 (Windows 7 Ult) - 32bit
 
| Name | Version | Remark |
|:-------|:-------|:-------:|
| Python | 2.7 | 32bit |
| Winlogbeat | 7.11.2 | - |
| Sysmon | 13.10 | - |
| RTA | - | - |

 * Guest 2 (Ubuntu 18.04) - 64bit
 
| Name | Version | Remark |
|:-------|:-------|:-------:|
| Python | 3.6.9 | 64bit |
| Elastalert | 0.2.4 | - |


##  [How to install](#index)
* Elastic Stack 64bit (Server 환경) - Host
  - Elastic Elasticsearch 설치 (version : 7.11.2)
    > https://www.elastic.co/kr/downloads/elasticsearch <br>
    > 네트워크 정보(포트 및 IP) 변경 필수 <br>
    > 설치폴더\config\elasticsearch.yml <br>
    > network.host: 호스트IP <br>
    > discovery.seed_hosts: ["127.0.0.1","[::1]"](이건 이대로 적어야함)

  - Elastic Kibana 설치 (version : 7.11.2)
    > https://www.elastic.co/kr/downloads/kibana
    > 네트워크 정보(포트 및 IP) 변경 필수 <br>
    > 설치폴더\config\kibana.yml
    > server.host: "호스트IP" <br>
    > elasticsearch.hosts: ["http://호스트IP:9200"]
 
  - Elastic Logstach (Optional) 설치
    > https://www.elastic.co/kr/downloads/logstash
  
  - Slack 설치 (아래 참조)
  
 
* Windows 7 32bit (Endpoint 환경) - VM 구성
  - Python 2.7 32bit
  - Elastic Winlogbeat 7.11.2
    > 1. C:program files에 Winlogbeat 파일이름 설정후 저장후  
    > 2. powershell(관리자) 실행 후 폴더경로 들어가서
    >    .\install-service-winlogbeat.ps1 명령 실행  
    > 3. .\winlogbeat.exe setup -e'  
    >    -E output.elasticsearch.hosts=['현재 호스트IP:9200']'  
    >    -E output.kibana.host=현재 호스트 IP:5601  
    > 4. Start-Service winlogbeat  
    > 5. .\winlogbeat.exe setup -dashboards
    > > ※설치폴더\winlogbeat.yml 필수 변경 <br>
    > > kibana - host: "호스트IP:5601" <br>
    > > elasticsearch - hosts: ["호스트IP:9200"] <br>
    > > 변경후 이미 서비스가 실행중이라면 <br>
    > > 서비스 들어가 winlogbeat 서비스를 다시 시작함
  - SwiftOnSecurity의 sysmon 13.10 (보안로그 발생을 위한 sysmon 환경 파일)
    > https://docs.microsoft.com/ko-kr/sysinternals/  
    > 1. sysmonconfig-export.xml파일을 sysmon파일 안에 넣어줌
    > >  ※없을경우 현 github 내 파일 다운로드후 진행
    > 2. cmd(관리자)실행 후 sysmon파일로 경로 이동해주고 명령문 입력  
    > >  Sysmon.exe -accepteula -i <br>
    > >  C:\Sysmon\sysmonconfig-export.xml -l -n 
  - Red Team Automation (Red Team용 MITRE ATT@CK 기반 malicious attack 발생)
    > https://github.com/endgameinc/RTA
    

* Ubuntu 18.04 64bit 환경
  - python 3.6 version 설치
    > apt install python3 <br>
    > sudo apt install python3-pip
  - elastalert 설치
    > https://elastalert.readthedocs.io/en/latest/running_elastalert.html <br>
    > 본 과정은 pip3 install이 아닌 git clone 으로 설치 <br>
    > 참조 https://sg-choi.tistory.com/309 
  
  - siga 설치 (아래 참조)

##  [How to run](#index) 

* Win7sp1 sysmon vm 환경 실행

* Win7sp1 sysmon vm 환경에서 sysmon 서비스 실행(관리자 계정)
  > sysmon.exe -i %configfile%
    (기존에 설치했다면 필요 없음)

* Win7sp1 sysmon vm 환경에서 winlogbeat 실행(관리자 계정)
  > winlogbeat.exe -c winlogbeat.yml
  
* Elasticsearch 실행(관리자 계정)
  > bin/elasticsearch.bat

* Elastic Kibana 실행(관리자 계정)
  > bin/kibana.bat

* Ubuntu 18.04 64bit 환경에서 Elasticalert 실행
  >/elastalert  
  >$python3 -m elastalert.elastalert --config config.yaml --verbose --rule example_rules\example_frequency.yaml <br>
  > ※ 본 github 내에 있는 example_frequency.yaml 사용가능

### sigma_rule (ubuntu_18.04 vm환경)
* sigmatools 설치
  > sudo pip3 install sigmatools <br>
* sigma git 불러오기
  > git clone http://github.com/Neo23x0/sigma.git <br>
  > cd sigma <br>
* home 디렉터리 복사
  > cp -r rules/windows [원하는 경로] 
* 실행
  > $sudo sigmac -t elastalert -r -c winlogbeat ~/windows -o [위에서 복사한 경로]/[설정파일이름]  
  > $cd ~  
* 실행 후 생성된 파일 원하는 폴더로 옮기기
  > $csplit --prefix sigma_ --suffix-format "%04d.yml" [설정파일이름] "/^alert:/" "{\*}"  
  > $mv ./sigma_\*.yml elastalert/example_rules  

### slack (HOST 환경)  
* slack 다운로드
  > https://slack.com/intl/ko-kr/downloads/windows  
  > 로그인 후 워크스페이스 생성 (이하 elk)  
* Incoming WebHook 설정
  > 1. slack 프로그램 내 만든 워크스페이스 접속  
  > 2. slack 찾아보기 -> 앱 -> incoming webhook 검색 후 추가  
  > 3. 홈페이지 자동 접속 -> slack에 추가 버튼 클릭 -> 메세지를 띄울 채널 선택  
  > 4. 수신 웹후크 통합 앱 추가 버튼 클릭  
  > 5. 웹후크 URL 복사해놓기
  > 6. 창 닫아도 접속 가능 : https://XXXXX(만든 워크스페이스의 이름).slack.com/services/new/incoming-webhook  
* ubuntu 실행
  > $cd elastalert/example_rules/  
  > example_frequency.yaml 파일 수정 (계속 써왔던 rule 파일)  
* rule 파일 수정
  > 아래 내용을 rule 파일 마지막에 입력  
  > ```
  > alert:  
  > - slack:  
  >      slack_webhook_url: "(webhook에서 복사한 url)"  
  >      slack_username_override: "(메세지를 올릴 유저명(ex.bboksill))"  
  >      slack_channel_override: "#(webhook에서 설정한 메세지 받을 채널이름(ex.work))"  
  >      slack_emoji_override: ":(메세지를 올릴 유저의 이모지(ex.zap)):"  
  >      slack_msg_color: "(메세지 색깔(ex.danger))"  
  > ```  
  > ※ 앞에 공백은 tab키 사용하지 않고 space로 공백을 만듬
* elastalert 실행  
  > elastalert --verbose --config config.yaml --rule example_rules\example_frequency <br>
  > window7에서 rta를 실행시키면 slack에 메세지 발생  

* 참고사이트
  > https://miiingo.tistory.com/223

## [Manual](#index)

* sysmon
  > https://github.com/trustedsec/SysmonCommunityGuide/blob/master/Sysmon.md

* elastic
  > https://www.elastic.co/guide/en/elastic-stack-get-started/7.6/get-started-elastic-stack.html#install-elasticsearch

* elastalert
  > https://elastalert.readthedocs.io/en/latest/running_elastalert.html
  
 ## [Trouble shooting](#index) 
 [[ windows 7 ]]
 * sysmon 10.x 실행 오류
   > kb2533623 설치 (wevtapi.dll 문제)
   
   > kb3033929 설치

* sysmon-config.xml

  **변경전** 
     
    > \<PipeEvent onmatch="exclude"\>
	
    > \<EVENTID condition="is"\>1\</EVENTID\> 
     
    > \<\/PipeEvent\>
          
   **변경후**   
   
     > \<PipeEvent onmatch="include"\>
			
     >**삭제**
	
     > \</PipeEvent\>
          
   **변경전**
   
     > \<WmiEvent onmatch="include"\>
		
     >    \<Operation condition="is">Created</Operation\> 
            
     > \</WmiEvent\>
           
   **변경후**     
   
     > \<WmiEvent onmatch="include"\>
	
     > **삭제** 
	
     > \</WmiEvent\>
        
[[ Elasticsearch ]] 
* network.host 설정 bootstrap checks failed
  > https://soye0n.tistory.com/178


[[ Elastalert ]]
* pip install 오류
  > python version 3.6 설치
* git 오류
  > $sudo apt install git
* "python setup.py egg_info" failed with error code 1
  > $sudo pip3 install --upgrade pip
* AttributeError: module 'yaml' has no attribute 'FullLoader'  
  > $pip3 install -U PyYAML  
* elastalert 룰 실행 시 vm -> host로 timed out 뜰 시
  > wifi를 사용하는 지 확인하고, 공용 네트워크로 연결되어 있는 지 확인. 개인 네트워크로 사용해야     timed out이 뜨지 않음. 
##  목표 구성도 참고 
* Open Source Endpoint monitoring 
  - https://github.com/DearBytes/Opensource-Endpoint-Monitoring
  
## Contributors
* maxup37
* idk3669
* air83
* bboksill
