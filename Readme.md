## Dependencias

Para poder ejecutar esta prueba de concepto debes tener maven instalado en tu computador.

Para instalar mave en macOS:
```bash
$ brew install maven
```
Para instalar mave en Ubuntu:
```bash
$ sudo apt install maven
```

## Ejectutar

Primero ingresamos a la carpeta del proyecto.
```bash
$ cd kafka
```
Para poder ejecutar nuestro ejemplo es necesario tener un servidor ZooKeeper y un servidor Kafka corriendo.
Primero corremos un servidor ZooKeeper en una ventana de la consola:
```bash
$ bin/zookeeper-server-start.sh config/zookeeper.properties
```
En otra ventana corremos un servidor Kafka
```bash
$ bin/kafka-server-start.sh config/server.properties
```
Ahora en otra ventana ccreamos los topic donde ingresaremos nuestro input y recibiremos el output.
Para el Input:
```bash
$ bin/kafka-topics.sh --create \
    --zookeeper localhost:2181 \
    --replication-factor 1 \
    --partitions 1 \
    --topic streams-plaintext-input
```
Para el Output:
```bash
$ bin/kafka-topics.sh --create \
    --zookeeper localhost:2181 \
    --replication-factor 1 \
    --partitions 1 \
    --topic streams-wordcount-output \
    --config cleanup.policy=compact
```
Ahora ingresamos a la carpeta donde esta nuestro ejemplo de Kafka Streams
```bash
$ cd prueba-kafka-streams
```
Compilamos el ejemplo y lo corremos:
```bash
$ mvn clean package
$ mvn exec:java -Dexec.mainClass=myapps.WordCount
```
En otra ventan volvemos a la carpta principal y corremos el conlose producer sobre el topic de input, donde podemos ingresar nuestros inputs:
```bash
$ cd ..
$ bin/kafka-console-producer.sh --broker-list localhost:9092 --topic streams-plaintext-input
```
En otra ventana corremos el console consumer sobre el topic de output para poder ver el output de nuestro programa:
```bash
$ bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 \
    --topic streams-wordcount-output \
    --from-beginning \
    --formatter kafka.tools.DefaultMessageFormatter \
    --property print.key=true \
    --property print.value=true \
    --property key.deserializer=org.apache.kafka.common.serialization.StringDeserializer \
    --property value.deserializer=org.apache.kafka.common.serialization.LongDeserializer
```