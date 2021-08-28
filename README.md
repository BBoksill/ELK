# Intelligent Incident Response Platform
## Index
- [Diagram](#Diagram)
- [Preference](#Preference)
- [How to install](#How-to-install)
- [How to run](#How-to-run)
- [Manual](#Manual)
- [오류수정](#오류수정) 


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
    > https://www.elastic.co/kr/downloads/elasticsearch

  - Elastic Kibana 설치 (version : 7.11.2)
    > https://www.elastic.co/kr/downloads/kibana
 
  - Elastic Logstach (Optional) 설치
    > https://www.elastic.co/kr/downloads/logstash
 
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
    > 본 과정은 pip3 install이 아닌 git clone 으로 설치

##  [How to run](#index) 

* Win7sp1 sysmon vm 환경 실행

* Win7sp1 sysmon vm 환경에서 sysmon 서비스 실행(관리자 계정)
  > sysmon.exe -i %configfile%
    (기존에 설치했다면 필요 없음)

* Win7sp1 sysmon vm 환경에서 winlogbeat 실행(관리자 계정)
  > winlogbeat.exe -c winlogbeat.yml

* Elastic Kibana 실행(관리자 계정)
  > bin/kibana.bat

* Elasticsearch 실행(관리자 계정)
  > bin/elasticsearch.bat

* Ubuntu 18.04 64bit 환경에서 Elasticalert 실행
  >/elastalert  
  >elastalert --verbose --start  --config <config.yaml> --rule <error.yaml>
 
## [Manual](#index)

* sysmon
  > https://github.com/trustedsec/SysmonCommunityGuide/blob/master/Sysmon.md

* elastic
  > https://www.elastic.co/guide/en/elastic-stack-get-started/7.6/get-started-elastic-stack.html#install-elasticsearch

* elastalert
  > https://elastalert.readthedocs.io/en/latest/running_elastalert.html
  
 ## [오류수정](#index) 
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
  > python version 3.6 다운

##  목표 구성도 참고 
* Open Source Endpoint monitoring 
  - https://github.com/DearBytes/Opensource-Endpoint-Monitoring
  
## Contributors
* maxup37
* idk3669
* air83
* bboksill
