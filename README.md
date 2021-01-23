# ksqldb-tutorial
ksqlDB

## documentation pages

https://docs.ksqldb.io/en/latest/concepts/ksqldb-and-kafka-streams/

https://docs.confluent.io/platform/current/ksqldb/tutorials/index.html

https://kafka-tutorials.confluent.io/?_ga=2.160960507.738662664.1611252233-174254943.1609342288

## getting started

start docker compose stack

`docker-compose up`

## console consumer & producer

start bash on broker

`docker-compose exec broker bash`

start key/value producer

`kafka-console-producer --topic example-topic --broker-list broker:9092 --property parse.key=true --property key.separator=":"`

start consumer

`kafka-console-consumer --topic example-topic --bootstrap-server broker:9092 --from-beginning`

start key/value consumer

`kafka-console-consumer --topic example-topic --bootstrap-server broker:9092 --from-beginning --property print.key=true --property key.separator="-"`

## ksqlDB cli

`docker exec -it ksqldb-cli ksql http://ksqldb-server:8088`

## ksqlDB statements

create order stream

```
CREATE STREAM ORDERS (order_id VARCHAR, product_name VARCHAR, number INT, total_price DOUBLE) WITH (kafka_topic='ORDERS', value_format='json', partitions=1);
```

add orders

```
INSERT INTO ORDERS (order_id, product_name, number, total_price) VALUES ('1', 'book', 3, 57.66);
	
INSERT INTO ORDERS (order_id, product_name, number, total_price) VALUES ('2', 'book', 2, 13.50);
	
INSERT INTO ORDERS (order_id, product_name, number, total_price) VALUES ('3', 'laptop', 1, 1000.99);
	
INSERT INTO ORDERS (order_id, product_name, number, total_price) VALUES ('4', 'laptop', 2, 2000.00);
	
INSERT INTO ORDERS (order_id, product_name, number, total_price) VALUES ('5', 'book', 6, 110.99);

INSERT INTO ORDERS (order_id, product_name, number, total_price) VALUES ('6', 'headset', 1, 99.99);
	
INSERT INTO ORDERS (order_id, product_name, number, total_price) VALUES ('7', 'laptop', 1, 500.99);
	
INSERT INTO ORDERS (order_id, product_name, number, total_price) VALUES ('8', 'book', 2, 20.17);

INSERT INTO ORDERS (order_id, product_name, number, total_price) VALUES ('9', 'headset', 5, 400.14);
	
INSERT INTO ORDERS (order_id, product_name, number, total_price) VALUES ('10', 'book', 1, 5.99);
	
INSERT INTO ORDERS (order_id, product_name, number, total_price) VALUES ('11', 'book', 5, 55.00);

INSERT INTO ORDERS (order_id, product_name, number, total_price) VALUES ('12', 'book', 2, 15.00);
```

create aggregation table

```
CREATE TABLE PRODUCT_TOTAL_PRICE AS SELECT product_name, SUM(total_price) AS sum_total_price FROM ORDERS GROUP BY product_name;
```
	
create order payments

```
CREATE STREAM PAYMENTS (order_id VARCHAR, payment_type VARCHAR) WITH (kafka_topic='PAYMENTS', value_format='json', partitions=1);
```
	
create joined stream

```	
CREATE STREAM ORDERS_AND_PAYMENTS AS
    SELECT * FROM ORDERS INNER JOIN PAYMENTS 
		WITHIN 7 DAYS 
		ON ORDERS.order_id = PAYMENTS.order_id;
```
		
add orders and payments

```
INSERT INTO PAYMENTS (order_id, payment_type) VALUES ('1', 'Paypal');
```
   
