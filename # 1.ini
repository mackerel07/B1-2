# 1. root 계정에서 agent-admin 계정으로 전환
su - agent-admin

# 2. 환경변수 설정 (현재 세션 적용)
export AGENT_HOME="/home/agent-admin/agent-app"
export AGENT_PORT="15034"
export AGENT_UPLOAD_DIR="$AGENT_HOME/upload_files"
export AGENT_KEY_PATH="$AGENT_HOME/api_keys"
export AGENT_LOG_DIR="$AGENT_HOME/logs"

# 미션을 위한 초기 제어 변수 
export MEMORY_LIMIT="256"
export CPU_MAX_OCCUPY="80"
export MULTI_THREAD_ENABLE="true"

# 3. 필수 디렉토리 생성 (-p 옵션으로 없으면 생성)
mkdir -p $AGENT_UPLOAD_DIR
mkdir -p $AGENT_KEY_PATH
mkdir -p $AGENT_LOG_DIR

# 4. secret.key 파일 생성 및 내용(agent_api_key_test) 입력
echo "agent_api_key_test" > $AGENT_KEY_PATH/secret.key #지정한 파일에 덮어쓰기

# 5. 정상적으로 적용되었는지 환경변수 확인
env | grep AGENT

AGENT_UPLOAD_DIR=/home/agent-admin/agent-app/upload_files 
AGENT_PORT=15034 AGENT_KEY_PATH=/home/agent-admin/agent-app/api_keys 
AGENT_HOME=/home/agent-admin/agent-app 
AGENT_LOG_DIR=/home/agent-admin/agent-app/logs

# agent-leak-app.py(메모리 누수 임의생성)

#OOM 분석
$AGENT_HOME/bin/monitor.sh 
python3 /home/agent-admin/agent-app/agent-leak-app.py

====== SYSTEM MONITOR RESULT ====== 
[HEALTH CHECK] Checking process 'agent_app.py'... [OK] (PID: 4993) 
Checking port 15034... [OK] 
[FIREWALL CHECK] 
[WARNING] UFW firewall inactive 
[RESOURCE MONITORING] 
CPU Usage : 0.0% 
MEM Usage : 3.5% 
DISK Used : 3% [INFO] 
Log appended: /var/log/agent-app/monitor.log 
agent-admin@c10c3d24140c:~$ python3 /home/agent-admin/agent-app/agent-leak-app.py 
[INFO] agent-leak-app 시작 (PID: 13293) 
[INFO] 설정된 MEMORY_LIMIT: 256MB 
[INFO] 서비스 실행 중... (데이터 적재 시작)  
[CRITICAL] [MemoryGuard] Memory limit exceeded (258MB >= 256MB) / (Recommend Over 256MB) 
[CRITICAL] [MemoryGuard] Self-terminating process 13293 to prevent system instability. 
>>> [SYSTEM] SELF-TERMINATED (Memory Limit Exceeded) <<< 
Killed

#검증 테스트
export MEMORY_LIMIT=512
python3 /home/agent-admin/agent-app/agent-leak-app.py

# agent-leak-app2.py(CPU 과점유 임의생성)

python3 /home/agent-admin/agent-app/agent-leak-app2.py
[INFO] agent-leak-app 시작 (PID: 13502)
[INFO] 설정된 CPU_MAX_OCCUPY: 80%
[INFO] 서비스 실행 중... (복잡한 연산 처리 시작)
[CRITICAL] [Watchdog] CPU usage exceeded threshold (80%)
[CRITICAL] [Watchdog] Terminating process 13502 to prevent system freeze.
>>> [SYSTEM] WATCHDOG: INITIATING EMERGENCY ABORT (SIGTERM) <<<
Terminated

#검증 테스트
export CPU_MAX_OCCUPY=100
python3 /home/agent-admin/agent-app/agent-leak-app2.py

# agent-leak-app3.py(교착상태 임의생성)
export MULTI_THREAD_ENABLE=true
python3 /home/agent-admin/agent-app/agent-leak-app3.py

[Thread-1] 자원 A 획득 완료, 작업 중...
[Thread-2] 자원 B 획득 완료, 작업 중...
[Thread-1] 자원 B 요청 [WAITING... BLOCKED]
[Thread-2] 자원 A 요청 [WAITING... BLOCKED]

root@c10c3d24140c:/# ps -ef | grep agent
agent-a+    4993       1  0 14:11 pts/1    00:00:04 python3 agent_app.py
root       12849   12754  0 18:59 pts/3    00:00:00 su - agent-admin
agent-a+   12850   12849  0 18:59 pts/3    00:00:00 -bash
agent-a+   13888   12850  0 19:32 pts/3    00:00:00 python3 /home/agent-admin/agent-app/agent-leak-app3.py
root       13959   13949  0 19:34 pts/4    00:00:00 grep --color=auto agent

# 검증 테스트

export MULTI_THREAD_ENABLE=false
python3 /home/agent-admin/agent-app/agent-leak-app3.py

[INFO] agent-leak-app 시작 (PID: 14053)
[INFO] 설정된 MULTI_THREAD_ENABLE: False
[INFO] 싱글스레드(안전) 모드 실행 중... (순차 처리 시작)

[Main] 자원 A 획득 및 작업 완료
[Main] 자원 B 획득 및 작업 완료
[INFO] 모든 작업이 정상적으로 완료되었습니다.