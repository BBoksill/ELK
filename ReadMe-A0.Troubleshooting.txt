[[ windows 7 ]]
* sysmon 10.x ���� ����
  > kb2533623 ��ġ (wevtapi.dll ����)
  > kb3033929 ��ġ

* sysmon-config.xml
  ** ������ 
       >  <PipeEvent onmatch="exclude">
	
	     <EVENTID condition="is">1</EVENTID> 
     
           </PipeEvent>
	

  > <PipeEvent onmatch="include">
									   
     </PipeEvent> 




[[ Elasticsearch ]] 
* network.host ���� bootstrap checks failed
  > https://soye0n.tistory.com/178



