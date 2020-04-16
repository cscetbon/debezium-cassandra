1. Start containers
```
docker-compose up
```
2. Initialize the keyspace
```
docker-compose exec -d cassandra-seed /opt/cassandra/tools/bin/cassandra-stress write n=1 cl=one -mode native cql3 user=cassandra password=cassandra

docker-compose exec -d cassandra-seed cqlsh -e "ALTER TABLE keyspace1.standard1 with cdc=true"
```
3. Start debezium
```
for name in cassandra-seed cassandra1 cassandra2; do docker-compose exec -d $name sh start-debezium.sh; done
```
This can take some time as cassandra takes some time to start. 

4. Make changes
```
docker-compose exec -d cassandra-seed /opt/cassandra/tools/bin/cassandra-stress write n=10M cl=one -mode native cql3 user=cassandra password=cassandra
```

5. Verify events on debezium logs
```
for name in cassandra-seed cassandra1 cassandra2; do docker-compose exec $name cat debezium.stdout.log | grep -i "Received event"; done
```
