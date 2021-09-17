#  지능형 사고 대응 플랫폼

##  목표 구성도 참고 
* Open Source Endpoint monitoring 
  - https://github.com/DearBytes/Opensource-Endpoint-Monitoring
  
##  시스템 구성도 

   ![screenshot](Conceptual_diagram.jpg)

##  환경 구성 
* Host PC

  * ElasticSearch 7.11.2
  	* https://www.elastic.co/kr/downloads/past-releases#elasticsearch
  * Kibana 7.11.2(ElasticSearch와 동일 버전 사용)
  	* https://www.elastic.co/kr/downloads/kibana
  * Logstash(optional)
  	* https://www.elastic.co/kr/downloads/logstash
  
* Windows 7 32bit(endpoint 환경) - VM 구성
  * Python 2.7 32bit
  	* https://www.python.org/download/releases/2.7/
  * winlogbeat 7.11.2
  	* https://www.elastic.co/kr/downloads/past-releases#winlogbeat
  * sysmon v13.10
  	* https://docs.microsoft.com/en-us/sysinternals/downloads/sysmon
  * Red Team Automation(Red Team용 MITRE ATT@CK 기반 Malicious Attack 발생)
  	* https://github.com/endgameinc/RTA
  * SwiftOnSecurity의 sysmon-config(보안 로그 발생을 위한 sysmon 환경 파일)
  	* https://github.com/SwiftOnSecurity/sysmon-config
  
* Ubuntu 18.04 64bit - VM 구성
  * Elastalert v0.2.4(git clone 설치법)
  	> https://elastalert.readthedocs.io/en/latest/running_elastalert.html
  * Python 3.6
  
  	> apt install python3
  * pip3
   
  	> sudo apt install python3-pip
  
##  실행 방법 
 * Host PC
	* ElasticSearch // bin/elasticsearch.bat 관리자 계정으로 실행
	* Kibana // bin/kibana.bat 관리자 계정으로 실행
 
 * Windows7 sp1 32bit(VM)
 	* Windows7 sp1 sysmon vm 환경에서 sysmon 서비스를 관리자 계정으로 실행
	
		> sysmon.exe -i %configfile%(기존에 설치했다면 필요 없음)
	* Windows7 sp1 sysmon VM 환경에서 winlogbeat 관리자 계정으로 실행 
	
		> winlogbeat.exe -c winlogbeat.yml 첫 설치 후 서비스에 실행되어 있으면 생략 가능
 
 * Ubuntu 18.04 64bit(VM)
 	* Ubuntu 18.04 64bit 환경에서 Elasticalert 실행
 	
		> /elastalert													   
		  python3 -m elastalert.elastalert --verbose --start --config <config.yaml> --rule <예제파일>		       
		  <예제파일>에 들어갈 룰 파일 ▼ https://github.com/Yelp/elastalert/blob/master/example_rules/example_frequency.yaml
## 메뉴얼 

* sysmon
  > https://github.com/trustedsec/SysmonCommunityGuide/blob/master/Sysmon.md

* elastic
  > https://www.elastic.co/guide/en/elastic-stack-get-started/7.6/get-started-elastic-stack.html#install-elasticsearch

* elastalert
  > https://elastalert.readthedocs.io/en/latest/running_elastalert.html
  
## 환경

* ElasticSearch
	* Host PC
		> 설치 폴더\config\elasticsearch.yml
		> 									       
		> #-----Network-----	
		> 											  
		> network.host:(기본으로 설정된 9200 포트 사용할 예정이므로 주석제거 X)
		> 
		> #-----Discovery-----			
		> 									   
		> discovery.seed_hosts:["127.0.0.1, "[::1]"]
* Kibana
	* Host PC
	
		> 설치 폴더\config\kibana.yml
		> 
		> #server.port:5601(기본으로 설정된 포트니 변경하지 말 것)
		> 
		> server.host:"host pc ip"(kibana를 실행한 PC의 IP로 설정)
		> 
		> elastcsearch.hosts:["http://[elasticsearch를 실행판 PC의 IP]:[포트]"]
		> 
		> elasticsearch를 실행할 호스트 PC의 IP 설정
* Winlogbeat
	* Windows7 sp1 VM
	
		> #-----Elasticsearch Output-----
		> 
		> hosts:["HOST PC IP:PORT"]
		> 
		> winlogbeat가 실행되고 있는 경우엔 다시 시작
		
* Elastalert
	* Ubuntu 18.04 VM
	 
		> git clone elastalert 디렉토리 내 /config.yaml//config.yaml이 없다면 config.yaml.example을 복사한 후 .example을 지우고 사용
		> 
		> es_host:[호스트 PC IP]
		> 
		> rules_folder:rule이 들어간 디렉토리 지정
		> 
		> --rule 속성을 사용하게 되면 룰 파일 하나만 지정하고, --rule 속성을 주지 않으면 rules_folder에 정해진 디렉토리가 기본으로 지정되어 디렉토리 내 룰들을 로드

* Slack
	* Slack 설치 
	
		> https://slack.com/intl/ko-kr/downloads/windows
	* Slack App 생성 
	
		> https://api.slack.com/apps
		> 
		> 새로운 워크스페이스 생성
		> 
		> --incoming WebHook 설정--
		> 
		> 1) https://XXXX(생성한 워크스페이스 이름).slack.com/services/new/incoming-webhook 접속
		> 
		> 2) 새로 만든 워크스테이션에서 Slack 찾아보기 ▶ 앱 ▶ WebHook 검색 후 구성 및 추가
		> 
		> 메시지를 표시할 채널을 선택 ▶ webhook URL 표시 ▶ Ubuntu VM 실행 ▶ elastalert rule 디렉토리에 들어가 임의로 실행할 룰 파일을 고른 뒤 아래 양식에 맞춰 맨 아래줄에 입력
		> 
			alert: 	

			- slack: 

				slack_webhook_url: "(복사한 웹후크 URL)" 

				slack_username_override: "ZEUS" 

				slack_channel_override: "#(만든채널)" 

				slack_emoji_override: ":zap:" 

				slack_msg_color: "danger"
			
	* 앞에 있는 공백은 TAB을 사용하지 않고 SPACE를 사용해서 공백을 만든다
		 
	> 실행 // elastalert --verbose --config config.yaml --rule example_rules/eaxmple_frequency 설정된 룰에 따라 Alert를 발생시키면 Slack에 메시지 발생
			> 

* Sigma
	> sudo pip3 install sigmatools	
	> 											
	> git clone https://github.com/Neo23x0/sigma.git
	> 									
	> cd [sigma folder]				
	> 									
	> cp -r rules/windows [복사할 경로] // 폴더 복사	
	> 									
	> sudo sigmac -t elastalert -r -c winlogbeat [복사한 경로] -o [원하는 경로]/[설정한 파일 이름]	
	> 			
	> -t 옵션 : elastalert을 타겟으로 설정	
	> 										
	> -c 옵션 : winlogbeat 인덱스로 설정	
	> 										
	> -o 옵션 : 원하는 파일 이름으로 설정되어 나옴	
	> 									
	> -r 옵션 : 디렉토리 내 서브 디렉토리까지 전부	
	> 									
	> 설정된 파일 하나에 룰이 저장되고, 룰 파일 내에 여러 룰이 있을 경우 마지막 룰만 인식이 되므로 나눠줘야 함	
	> 		     
	> csplit --prefix sigma_ --suffix-format "%04d.yml" rule "/^alert:/" "{*}" (sigmac 명령어를 통해 나온 파일이 있는 폴더에서 해야하며 많은 파일들이 생성될 예정)		
	> 											
	> sigma_000.yml 파일로 여러개가 저장되고 전부 elastalert 내 rule 폴더로 파일 이동						
	
 ## 오류 수정 
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
 * Winlogbeat
 	> config 설정 후 kibana에 sysmon 로그 미발생
 	> 작업관리자 ▶ winlogbeat 다시 시작
        
[[ Elasticsearch ]] 
* network.host 설정 bootstrap checks failed
  > https://soye0n.tistory.com/178

[[Ubuntu]]
* pip 설치 오류
	> python 3.6 다운
	> python 실행이 안될 시 pip 오류 설정 확인 후 python3 버전 설치
	> python3 실행 후에도 되지 않는다면 기본으로 설정된 python 버전으로 설정
* urllib3(1.25.2) or chardet(3.0.4) 오류
	> pip3 install request
* module 'yaml' has no attribute 'FullLoader' 오류
	> pip3 install -U PyYAML
* pkg_resources.DistributionNotFound:The 'elasticsearch' distribution was not found and is required bt elastalert 오류
	> sudo pip3 install "elasticsearch>=5.0.0,<7.0.0"
* importError:cannot import name main
	> pip3 install "ealsticsearch>=5.0.0,<7.0.0"
* elastalert 룰 실행 시 VM ▶ Host Timed out 오류 발생
	> WIFI 사용 중인지 확인 ▶ 공용 네트워크 연결 확인 ▶ 개인 네트워크로 사용하면 Timed out 뜨지 않음
	
## Contributors
* maxup37
* idk3669
* air83
