# FBID

A continuación se mostrara una lista con los puntos que se han realizado marcados y su correspondiente puntuación:

- [x] [4ptos] Lograr el funcionamiento de la práctica sin realizar modificaciones
- [x] [1pto] Ejecución del job de predicción con Spark Submit en vez de IntelliJ
- [x] [1pto] Dockerizar cada uno de los servicios que componen la arquitectura completa
- [x] [1pto] Desplegar el escenario completo usando docker-compose
- [x] [1pto] Desplegar el escenario completo en Google Cloud/AWS
- [x] [2ptos] Desplegar el escenario completo usando kubernetes
- [ ] [2ptos]  Entrenar el modelo con Apache Airflow

## Lograr el funcionamiento de la práctica sin realizar modificaciones

Primero de todo se ha decidido utilizar el sistema operativo Ubuntu debido a las recomendaciones de los profesores, por lo que se deplego una maquina virtual con este sistema operativo instalado.
En dicha máquina virtual, se debe instalar la siguiente lista de programas con sus respectivas versiones:

- Intellij (jdk_1.8)
- Pyhton3 (Suggested version 3.7)
- PIP
- SBT
- MongoDB
- Spark (Mandatory version 3.1.2)
- Scala(Suggested version 2.12)
- Zookeeper
- Kafka (Mandatory version kafka_2.12-3.0.0)
- Las respectivas librerias de python indicadas en el fichero requirements.txt


### Iniciar Zookeeper
Abriremos un terminal.
Antes de iniciar zookeper debemos situarnos en el directorio de kafka.
Desde el directorio principal del programa ejecutaremos el siguiente comando:
```
   cd Kafka/kafka_2.12-3.0.0/
  ```
Una vez ahí, iniciamos Zookeeper con el siguiente comando:
 ```
   bin/zookeeper-server-start.sh config/zookeeper.properties
  ```
### Iniciar Kafka

Para iniciar Kafka deberemos hacer igual que con Zookeper, ir al directorio de Kafka y ejecutar el siguiente comando:
  
  ```
    bin/kafka-server-start.sh config/server.properties
   ```
Para crear un Topic deberemos ir al directorio de Kafka y ejecutar el siguiente comando:

  ```
      bin/kafka-topics.sh \
        --create \
        --bootstrap-server localhost:9092 \
        --replication-factor 1 \
        --partitions 1 \
        --topic flight_delay_classification_request
   ```
 Nos debera aparecer el siguiente mensaje confirmandonos que el Topic se ha creado correctamente:
   ![image](https://user-images.githubusercontent.com/85503582/141807720-93b00945-6d96-4edb-b59e-a2085c8bb295.png)

 Podemos ver la lista de Topics creados con el siguiente comando:
  ```
      bin/kafka-topics.sh --bootstrap-server localhost:9092 --list
  ```
  Y como vemos de nuevo, se ha creado correctamente:
  ![image](https://user-images.githubusercontent.com/85503582/141808087-1043e657-3c39-4768-8a1b-89bfbdf916fc.png)

  
  
  
