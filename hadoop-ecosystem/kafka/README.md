# Kafka

3.9 버전을 사용하며, kraft 를 사용한다. (zookeeper 제거)

## Installation

<https://github.com/DevDooly/kafka>

```shell
> ansible-playbook -i inventories/cluster01 kafka-playbook.yml -b -K -e "profile=cluster01"
```
## FEATURE

### KAFKA

```yaml
install-kafka
start-kafka
stop-kafka
restart-kafka
```

### KAFKA UI

```yaml
install-kafka-ui
start-kafka-ui
stop-kafka-ui
restart-kafka-ui
```

### SCHEMA REGISTRY

```yaml
install-schema-registry
```