# PIC

Windows
==========================================================

common
-----------------------------------------------------------
- ctrl+shift+esc  
    - 프로세스
    - 네트워킹
- eventvwr.msc  
    - 시스템 
    - 보안, 보안-속성
- diskmgmt.msc
- 네트워크 (제어판-네트워크 및 인터넷-네트워크 연결-네트워크 및 공유센터)  
    - 어뎁터 설정 변경
- cmd-ipconfig
- cmd-netstat -an
- 공유 및 저장소 관리 (storagemgmt.msc)
- 로컬 보안정책 (secpol.msc /s)  
    - 보안설정-계정정책-암호정책  
    - IP보안 정책
- 서버관리자(ServerManager.msc)  
    구성-로컬사용자 및 그룹-사용자(일반, 그룹)
- winver
- 제어판-windows update
- 제어판-날짜 및 시간-인터넷시간

nms(std)
--------------------------------------------------------------

nms(act)
--------------------------------------------------------------
- services.msc  
    - OracleServiceORA10G  
    - OracleOraDb10g_home1TNSListener
- Oracle SQL*Plus(trs_nms)  
    - SELECT * FROM T_SM_USER
- Oracle SQL*Plus(/as sysdba)  
    - SELECT USERNAME FROM DBA_USERS;  
    - SELECT USERNAME, ACCOUNT_STATUS FROM DBA_USERS;
- process 7
- process 7 log  
    - C:\trs_nms\log\app-sym.log
    - C:\trs_nms\log\app-sem.log
    - C:\trs_nms\log\app-pem.log
    - C:\trs_nms\log\app-fam.log
    - C:\trs_nms\log\app-com.log
    - C:\trs_nms\log\app-chm.log
    - C:\trs_nms\log\app-cam.log

- nms client  
    - 초기 화면, 서버모니터링, 장애이력조회, 채널모니터링, 실시간모니터링


trs-0
--------------------------------------------------------------
- services.msc
    - SQL Server (MSSQLSERVER)  
    - SQL Server Agent (MSSQLSERVER)
- SQL Management Studio 
    - table
- SQL Management Studio 
    - 보안-로그인
    - 보안-로그인-sa속성
- process
    - msa
    - msa console
    - ms
- log
    - C:\Program Files\Opendigm\MC\MC\Log\MC_Client.log 
    - C:\Program Files\Opendigm\MSA\Log\MSAProcess.log 
    - C:\Program Files\Opendigm\MS\Log\MS_Server.log 
- mc
    - 모뎀 검색
    - 모뎀 환경 설정
    - 모니터링
    
trs-1,2,3
--------------------------------------------------------------
- process
    - gcp
    - gma
    - gw
    - packet
- log
    - C:\Program Files\Opendigm\TDIS\GCP\LOG
    - C:\Program Files\Opendigm\TDIS\GMA\LOG
    - C:\Program Files\Opendigm\TDIS\GW\P00?
    - C:\Program Files\Opendigm\TDIS\PACKET\LOG
- gw

tbms
--------------------------------------------------------------
- services.msc
    - SQL Server (MSSQLSERVER)  
    - SQL Server Agent (MSSQLSERVER)
- SQL Management Studio 
    - table
- SQL Management Studio 
    - 보안-로그인
    - 보안-로그인-sa속성
- process
    - 서버프로그램
- log
    - C:\Program Files\opemdigm\TBMS\CLIENT\log\Client.Log 
    - C:\Program Files\opemdigm\TBMS\SERVER\log\Server.Log 
- tbms client
    - 초기 화면
    - RTU 알람이력

Linux
==============================================================

iems
---
- top
- df -k
- ps -ef
- cat /var/log/messages
- netstat -an
- ifconfig -a
- ntpq -p / ntpstat / ps -ef | grep ntp 
- ps -ef | grep oracle
- cat /oracle/product/11.2.0/db/network/log/sqlnet.log
- date
- cat /etc/passwd
- cat /etc/group
- su - oracle (sqlplus '/as sysdba')
    - SELECT USERNAME FROM DBA_USERS;
    - SELECT USERNAME, SCCOUNT_STATUS FROM DBA_USERS;
- cat /etc/login.defs | grep ^PASS
- cat /etc/logrotate.d/syslog
- cat /etc/*-release / last reboot
- service --status-all / ps -ef | grep 'mounted'

RedHat
==============================================================
common
---
- ps -ef
- df -k
- cat /var/adm/messages
- netstat -an
- ifconfig -a
- ps -ef | grep ntp / ntpq -p
- date
- cat /etc/passwd 
- cat /etc/group
- cat /etc/default/passwd 
- cat /etc/logadm.conf
- uname -s -r / showrev -p
- dfshares

nms(std)
---

nms(act)
---
- ps -ef | grep oracle
- cat /oracle/product/10.2.0/db_1/network/log/sqlnet.log
- su - oracle (sqlplus '/as sysdba')
    - SELECT USERNAME FROM DBA_USERS;
    - SELECT USERNAME, SCCOUNT_STATUS FROM DBA_USERS;
- ps -ef | grep java
- cat /home2/trs_nms/log/app-sem.log
- client
    - 초기 화면
    - 서버모니터링
    - 장애이력조회
    - 채널모니터링
    - 실시간모니터링

Disk Array
==============================================================
- /usr/sbin sccli
- show cache-parameters / show logical-drives
- show peripheral-device-status
- show frus
- show disks
- show controller-date / show inquiry-data
- show enclosure-status
- show events
- show network-parameters / show host-parameters

ref
===
find / -name 'something*'

# PIC
