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

### Train and Save de the model with PySpark mllib

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
 
 
### Run Flight Predictor

Primero, debemos cambiar el valor base_paht en la clase scala MakePrediction, cambie ese valor para la ruta donde se haya colocado el repositorio de la práctica:

![tempsnip](https://user-images.githubusercontent.com/85503582/141818494-1841da23-5218-48fb-8b1e-108774dd8a6e.png)

Ahora correremos el proyecto utilizando el programa intellij:

Con el siguiente comando abritemos la aplicación:
  ```
intellij-idea-community
  ```   
Corremos nuestro Script de Scala y como veremos se pondra todo en funcionamiento:

![image](https://user-images.githubusercontent.com/85503582/141821969-122e20d5-2970-449a-be2d-55e1ec05ef5a.png)


### Iniciar la aplicación web
  
 Primero, debemos enlazar la variable `PROJECT_HOME` con el path donde se encuentra el repositorio base de la prcatica, en nuestro caso:
   ```
  export PROJECT_HOME=/home/user/Desktop/practica_big_data_2019
   ```
Debemos al directorio "web" el cual es un subdirecctorio dentro de "resources" y ejecutar el Script "predict_flask.py" con los siguientes comandos:
  ```
  cd practica_big_data_2019/resources/web
  python3 predict_flask.py
  
 ```
Ahora vistamos la siguiente dirección http://localhost:5000/flights/delays/predict_kafka y comprobamos que todo funcione correctamente:
 
 ![tempsnip](https://user-images.githubusercontent.com/85503582/141823810-18688529-0310-4bcf-8827-f3bc64d39331.png)

  
### Verifique los registros de predicciones insertados en MongoDB
 
 Para ello corra los siguientes comandos:
 ```
   $ mongo
   > use agile_data_science;
   >db.flight_delay_classification_response.find();
  
  ```
 En nuestro caso se ha obtenido la siguiente salida:
  
 ![image](https://user-images.githubusercontent.com/85503582/141827695-92fc8d7a-5b19-473e-af8b-32a37777553a.png)

## Ejecución del job de predicción con Spark Submit en vez de IntelliJ
  
Para usar Spark-submit primero se necesitara compilar el código y crear un archivo JAR usando sbt, para ello se ejecutaran los siguientes comandos (desde el directorio base de la práctica):
 ```
   cd flight_prediction/
   sbt compile
   sbt package
  
  ```
Obteniendo la siguiente salida:
![image](https://user-images.githubusercontent.com/85503582/141834729-a696954b-05cd-4aab-b7e1-a179b625663f.png)

Finalmente deberemos correr el siguiente comando en el que se incluyen los 2 paquetes mencionados:

 ```
  /opt/spark/bin/spark-submit --class es.upm.dit.ging.predictor.MakePrediction  --packages org.mongodb.spark:mongo-spark-connector_2.12:3.0.1,org.apache.spark:spark-sql-kafka-0-10_2.12:3.1.2 /home/upm/Desktop/practica_big_data_2019/flight_prediction/target/scala-2.12/flight_prediction_2.12-0.1.jar
  ```
![image](https://user-images.githubusercontent.com/85503582/141835445-9d410bbb-f363-4ec2-a9aa-0d359bac1351.png)

  
  
  
  
  
  
  
  
  
  
