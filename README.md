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

Opcionalmente nos podemos habrir una consola con un suscriptor atendiendo a ese Topic que hemos creado y de esta manera ver los distintos mensajes que se van generando:
```
  bin/kafka-console-consumer.sh \
      --bootstrap-server localhost:9092 \
      --topic flight_delay_classification_request \
      --from-beginning
  ```
### Importar datos a Mongodb

Comprobamos que el Mongodb esta activo, ejecutando el siguiente comando:
   ```
service mongodb status
  ```
Nos debería aparecer el siguiente mensaje:
  
  ![image](https://user-images.githubusercontent.com/85503582/141809306-565ce5b2-a2e5-4b65-bc08-5b508062df58.png)

Corremos el script import_distances, ejecutando el siguiente comando desde el directorio base de la práctica:
  ```
./resources/import_distances.sh
  ```
Tras ello nos debe aparecer el siguiente mensaje:
![image](https://user-images.githubusercontent.com/85503582/141812302-db7ded92-12da-46ee-8a3e-837e89e6a89a.png)

## Train and Save de the model with PySpark mllib

Desde el directorio base deberemos realizar los siguientes pasos.
Enlazar la variable `JAVA_HOME` con el path donde se encuentra la instalación de Java, en nuestro caso:
  ```
    export JAVA_HOME=/usr/java-1.8.0-openjdk-amd64/bin
 ```
Enlazar la variable `SPARK_HOME` con el path donde se encuentra la instalación de Spark, en nuestro caso:
  ```
    export SPARK_HOME=/usr
  ```
Ahora se ejecuta el script `train_spark_mllib_model.py` para entrenar un modelo guardarlo:
  ```
      python3 resources/train_spark_mllib_model.py .
  ```
  Como resultado podremos comprobar que se han guardado una serie de archivos en la carpeta models: 
  
  ```
  ls ../models
  ```   
 ![image](https://user-images.githubusercontent.com/85503582/141814555-3a94a63b-d152-46b2-9854-9ebfddd18d0f.png)
 
 
## Run Flight Predictor

Primero, debemos cambiar el valor base_paht en la clase scala MakePrediction, cambie ese valor para la ruta donde se coloca el repositorio de clonación:

![tempsnip](https://user-images.githubusercontent.com/85503582/141818494-1841da23-5218-48fb-8b1e-108774dd8a6e.png)

Ahora correremos el proyecto utilizando el programa intellij:





  
Please, note that in order to use spark-submit you first need to compile the code and build a JAR file using sbt. Also, when running the spark-submit command, you have to add at least these two packages with the --packages option:
  ```
  --packages org.mongodb.spark:mongo-spark-connector_2.12:3.0.1,org.apache.spark:spark-sql-kafka-0-10_2.12:3.1.2
     
  ``` 
   Be carefull with the packages version because if you are using another version of spark, kafka or mongo you have to choose the correspondent version to your installation. This packages work with Spark 3.1.2, kafka_2.12-3.1.2 and mongo superior to 2.6
  
  
