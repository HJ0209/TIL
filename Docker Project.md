#  Docker Project

* volume : db-data는 postgres있는 폴더에만 볼륨마운트 걸면 가능
* localhost:8000 (투표 페이지) / localhost:5001 (결과페이지)
* redis : noSQL > 키밸류만 들어가 있는 시스템 
  * 간단하게 사용가능
* postgress : RDBS

* worker_ java로 만들어진 컨트롤러 (DB에서 데이터 확인해 전달)





SWARM 만들기 > STACK 1개 만들기 > 네트워크 2개 만들기 > 서비스 5개 만들기



1. `docker-compose`파일에서 manager port에 `5001:5001` 추가하기

   ```sql
   docker-compose up
   ```



2. 노드 설정하기

   ```sql
   PS C:\Users\HPE\work\docker\lab\2.swarm\swarm-app-1> docker exec -it manager sh
   / # docker swarm init
   Swarm initialized: current node (1ethzljjk1sjwuir4q0q1muub) is now a manager.
   
   To add a worker to this swarm, run the following command:
   
       docker swarm join --token SWMTKN-1-2czqq6weiryz98g6x1bn8za54ep0oolxozh74dul5f8fbvqqyb-864rfybopdbqbcj3nckkumlrd 172.19.0.3:2377
   
   To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.
   
   / # exit
   
   PS C:\Users\HPE\work\docker\lab\2.swarm\swarm-app-1> docker exec -it work01 docker swarm join --token SWMTKN-1-2czqq6wei
   ryz98g6x1bn8za54ep0oolxozh74dul5f8fbvqqyb-864rfybopdbqbcj3nckkumlrd 172.19.0.3:2377
   This node joined a swarm as a worker.
   
   PS C:\Users\HPE\work\docker\lab\2.swarm\swarm-app-1> docker exec -it work02 docker swarm join --token SWMTKN-1-2czqq6wei
   ryz98g6x1bn8za54ep0oolxozh74dul5f8fbvqqyb-864rfybopdbqbcj3nckkumlrd 172.19.0.3:2377
   This node joined a swarm as a worker.
   
   PS C:\Users\HPE\work\docker\lab\2.swarm\swarm-app-1> docker exec -it work03 docker swarm join --token SWMTKN-1-2czqq6wei
   ryz98g6x1bn8za54ep0oolxozh74dul5f8fbvqqyb-864rfybopdbqbcj3nckkumlrd 172.19.0.3:2377
   This node joined a swarm as a worker.
   
   PS C:\Users\HPE\work\docker\lab\2.swarm\swarm-app-1> docker exec -it manager sh
   / # docker node ls
   ID                            HOSTNAME            STATUS              AVAILABILITY        MANAGER STATUS      ENGINE VERSION
   1ethzljjk1sjwuir4q0q1muub *   1e8299b9ca52        Ready               Active              Leader              19.03.5
   odeurthvulg22lb2qopw4cm89     3ed897a64db4        Ready               Active                                  19.03.5
   aetsh9z0jum5jtsswne578p7u     024eec3ed5a3        Ready               Active                                  19.03.5
   n47kp0q0iepc0yl99qdjf3hiy     979ad63921b8        Ready               Active                                  19.03.5
   ```



3. 네트워크 설정하기

   ```sql
   # docker exec -it manager sh
   
   / # docker network create -d overlay backend
   l114p1iu8iv5bpwx7u0e5wxl6
   
   / # docker network create -d overlay frontend
   apyb7z1q7qf7g3o9uxnmje201
   
   / # docker network ls
   NETWORK ID          NAME                DRIVER              SCOPE
   l114p1iu8iv5        backend             overlay             swarm
   apyb7z1q7qf7        frontend            overlay             swarm
   ```

   

4. 서비스 만들기

   ```sql
   PS C:\Users\HPE\work\docker\lab\2.swarm\swarm-app-1> docker exec -it manager sh
   
   / # docker service create --name vote -p 80:80 --network frontend --replicas 2 bretfisher/examplevotingapp_vote
   
   / # docker service create --name redis --network frontend redis:3.2
   xpfj3v5be47s2ilv0ab5a2kx1
   
   / # docker service create --name worker --network frontend --network backend bretfisher/examplevotingapp_worker:java
   
   / # docker service create --name db --network backend --mount type=volume,source=db-data,target=/var/lib/postgresql/data
   
   / # docker service create --name result --network backend -p 5001:80 bretfisher/examplevotingapp_result
   ```

   * worker은 frontent, backend 모두 연결
   * 웹사이트로 접속해야 하는 vote-app과 result에만 port 번호 추가
   * db만 volume 지정
   * port 80은 웹에 관련된 것



5. 서비스확인

   * DB밖에 볼 수 없음

     ```sql
     docker serviece ps db
     
     docker exec -it [노드번호] sh
     docker ps > 현재 실행하고 있는 프로세스 확인
     
     docker exec -it [아이디] bash
     
     psql -Upostgres : 초기에 설정할 수 있는 것 
     
     
     postgres= # select * from votes; > 투표한게 보임
     ```

     

