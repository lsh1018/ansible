# ansible

MariaDB 운영 점검과 AWX 기반 자동 설치 예제를 담은 Ansible 저장소입니다.

## 포함된 플레이북

- [`mariadb_check.yml`](/workspaces/ansible/mariadb_check.yml): 기존 MariaDB 서비스 상태 점검용 playbook
- [`mariadb-awx/mariadb_install_awx.yml`](/workspaces/ansible/mariadb-awx/mariadb_install_awx.yml): Ubuntu 서버에 AWX로 MariaDB를 설치하고 초기 bootstrap까지 수행하는 playbook
- [`mariadb-awx/read.me`](/workspaces/ansible/mariadb-awx/read.me): AWX 실행 방법, 변수 예시, 버전별 설치 이슈 정리 문서
- [`oracle-awx/oracle19c_install_awx.yml`](/workspaces/ansible/oracle-awx/oracle19c_install_awx.yml): RHEL/Rocky 8/9 서버에 AWX로 Oracle Database 19c 엔진을 silent mode로 설치하는 playbook
- [`oracle-awx/read.me`](/workspaces/ansible/oracle-awx/read.me): Oracle 19c AWX 설치 전제조건, OS 사전 점검, RU 패치 주의사항 정리 문서

## 프로젝트 개요

이 프로젝트의 핵심 파일은 [`mariadb_check.yml`](/workspaces/ansible/mariadb_check.yml)이며, 대상 서버에서 다음 항목을 확인합니다.

- `systemctl is-active mariadb`로 MariaDB 서비스 활성 상태 확인
- `ss -lntp | grep 3306`으로 3306 포트 리슨 여부 확인
- `mysql --version`으로 설치된 MariaDB/MySQL 클라이언트 버전 확인

플레이북은 변경 작업 없이 상태를 조회하는 용도로 작성되어 있으며, 결과는 `debug` 태스크로 출력됩니다.

## 실행 예시

사전 조건:

- 제어 노드에 `ansible` 또는 `ansible-core`가 설치되어 있어야 합니다.
- 대상 서버에 SSH 접속 가능해야 합니다.
- 대상 서버에서 `systemctl`, `ss`, `mysql` 명령을 사용할 수 있어야 합니다.

### 인벤토리 파일을 사용하는 경우

```bash
ansible-playbook -i inventory.ini mariadb_check.yml
```

예시 `inventory.ini`:

```ini
[db]
db-server-01 ansible_host=192.168.0.10 ansible_user=ubuntu
```

### 단일 호스트를 직접 지정하는 경우

```bash
ansible-playbook -i "192.168.0.10," -u ubuntu mariadb_check.yml
```

### 로컬 환경에서 테스트하는 경우

```bash
ansible-playbook -i "localhost," -c local mariadb_check.yml
```

## 실행 결과 예시

정상 실행 시 아래와 같은 정보가 출력됩니다.

- MariaDB 서비스 상태
- 3306 포트 리슨 정보
- MariaDB 또는 MySQL 클라이언트 버전 정보

## AWX 자동 설치 문서

AWX에서 MariaDB 자동 설치를 진행하려면 [`mariadb-awx/read.me`](/workspaces/ansible/mariadb-awx/read.me)를 기준으로 Job Template과 Extra Variables를 구성하면 됩니다.

Oracle 19c 엔진 자동 설치를 진행하려면 [`oracle-awx/read.me`](/workspaces/ansible/oracle-awx/read.me)를 기준으로 Job Template과 설치 미디어 staging 경로를 구성하면 됩니다.
