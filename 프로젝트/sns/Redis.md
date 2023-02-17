# Redis vs Local Caching

![image-20230217164011409](md-images/image-20230217164011409.png)



# Redis

* In-memory 데이터베이스

* key-value 형태의 데이터베이스이다. 

* 다양한 command를 제공

* Single thread

* 여러개의 Redis를 하나처럼 사용가능

  * 휘발성 메모리라서 여러개의 Redis 사용함

  * 옵션 
    * Sentinel 
    * Redis Cluster



# 명령어

1. key - value
   1. SET : 저장
      1. EX : 기한 설정(초)
   2. GET : 읽기
2. key - subkey 
   1. HSET : 
   2. HGET
   3. HDEL
3. KEYS
4. score - member
   1. ZADD
   2. ZRANGE



# ref

https://redis.io/