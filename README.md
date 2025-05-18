# PgDog를 활용한 PostgreSQL 샤딩 및 트랜잭션 풀링 실습 노트

## ✅ 실습 목표
- PostgreSQL을 수평 확장하기 위한 샤딩 개념 이해
- PgDog를 통해 SQL 쿼리를 자동으로 샤드에 라우팅하는 원리 체험
- 트랜잭션 풀링 및 연결 재사용 확인

---

## 📦 실습 환경 구성

### 1. GitHub에서 PgDog 클론
```bash
git clone https://github.com/pgdogdev/pgdog
cd pgdog
```

### 2. Docker Compose로 샤드 및 PgDog 실행
```bash
docker-compose up --build
```

### 3. PostgreSQL 접속속
```bash 
psql -h localhost -p 6432 -U postgres
```

## 📦 실습 
### 🧪 실습 1: 자동 샤드 라우팅 확인
```bash 
-- users 테이블 생성 (PgDog가 각 샤드에 분산 생성)
CREATE TABLE users (
  id INT PRIMARY KEY,
  email TEXT
);

-- 데이터 삽입
INSERT INTO users (id, email) VALUES (1, 'one@acme.com');
INSERT INTO users (id, email) VALUES (2, 'two@acme.com');
INSERT INTO users (id, email) VALUES (3, 'three@acme.com');

```

### 각 샤드에서 확인
```bash 
docker exec -it pgdog-shard_0-1 psql -U postgres -c "SELECT * FROM users;"
docker exec -it pgdog-shard_1-1 psql -U postgres -c "SELECT * FROM users;"
docker exec -it pgdog-shard_2-1 psql -U postgres -c "SELECT * FROM users;"
```

### 🧪 실습 2: 트랜잭션 풀링
```bash 
-- PgDog가 트랜잭션 범위를 추적하여 커넥션을 안정적으로 관리함
BEGIN;
INSERT INTO users (id, name) VALUES (100, 'Zack');
COMMIT;
```

### PgDog 로그 확인
```bash 
docker logs pgdog
```


### 세션 자료
https://brook-entrance-893.notion.site/PgDog-PostgreSQL-1f619c566cd380f08543dc6d3b997efb?pvs=4
