# Redis-Cluster set up with docker-compose
## Mac not support. Mac doesn't have a docker0 network component. When redis-cli to localhost:7000, it wil redirect to 172.20.0.3 (dummy) and host not found.
## Prepare
1. install docker. version: 19.03.08.
2. install docker-compose. version: 1.25.5
3. Create docker network "redis-cluster_frontend" first, the docker-compose.yml will depend on this bridged network.
4. Run "docker-compose up" in current directory. 
	1. there will be 6 redis instances started up seperately. 
	2. [TODO] Set up docker container's hostname, for coming cluster setup steps usage. 
5. Run "docker network inspect redis-cluster_frontend" and take a note on the ipaddresses in result. 
6. Run "docker exec -it node-7000 bash" to login to docker terminal. 
7. Run "redis-cli --cluster create 172.18.0.2:6379..... --cluster-replicas 1" and "yes" on the promote. This will invite instances of certain ip address to join current cluster. 
8. RUN "cluster nodes" to check the instances in the cluster. 
> b67fa841f3e7592ed9fd1261ce5cd0c6d51512c7 172.18.0.5:6379@16379 slave 9655b72d3f0c8e356fa0bb94f611df2cc215ac7c 0 1595922286339 1 connected 
> 2676ab9e7503472d11bfbdb9e12481134de747 172.18.0.2:6379@16379 master - 0 1595922286000 3 connected 10923-16383 
> 95c0ca91103cacc4ee992b6f3c5db12c5dcd1a 172.18.0.6:6379@16379 slave 1d2676ab9e7503472d11bfbdb9e12481134de747 0 1595922286000 3 connected 
> 34c3163feee7c44f1f0a71c654748a8b6f8f54 172.18.0.7:6379@16379 myself,slave 21b3f68070ca0638978c6fe19c98a7ebaa50024 0 1595922287000 2 connected 
> b3f68070ca0638978c6fe19c98a7ebaa500024 172.18.0.4:6379@16379 master - 0 1595922288384 2 connected 5461-10922 
> 55b72d3f0c8e356fa0bb94f611df2cc215ac7c 172.18.0.3:6379@16379 master - 0 1595922287361 1 connected 0-5460 

9.  Test.
	1. "redis-cli -p 7000 -c set 111 222" 
	> OK
	2. "redis-cli -p 7003 -c get 111" 
	> OK
	3. redis-cli -p 7002 -c
	> set 222 233
	> redirect to **some other instance**
