![image-20230218190705808](md-images/image-20230218190705808.png)

* 데이터 처리

* Producer
  * 메시지 생성(key, data)
    * key : Partition idx
* Broker
  * 데이터 중개
  * partition : 데이터 담기는 큐(FIFO)
* Consumer 
  * parition에 담긴 데이터 처리