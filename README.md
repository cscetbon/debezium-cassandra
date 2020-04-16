1. Start containers
```
docker-compose up
```
2. Initialize keyspace
```
docker-compose exec -d cassandra-seed /opt/cassandra/tools/bin/cassandra-stress write n=1 cl=one -mode native cql3 user=cassandra password=cassandra
```
```
docker-compose exec -d cassandra-seed cqlsh -e "ALTER TABLE keyspace1.standard1 with cdc=true"
```
3. Start debezium
```
docker-compose exec cassandra-seed  sh start-debezium.sh; done
```
This can take some time as cassandra takes some time to start. 

4. Make changes
```
docker-compose exec cassandra-seed /opt/cassandra/tools/bin/cassandra-stress write n=5K cl=one -mode native cql3 user=cassandra password=cassandra
```

5. Force commitlog to be pushed to cdc folder
```
docker-compose exec cassandra-seed nodetool flush
```

6. Verify events on debezium logs
```
docker-compose exec cassandra-seed  cat debezium.stdout.log | grep -i "commit"; done
```
