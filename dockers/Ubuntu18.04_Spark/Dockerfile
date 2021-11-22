FROM ubuntu:18.04

#Configuración por defecto
RUN apt-get update && DEBIAN_FRONTEND=noninteractive apt-get install -y locales

RUN sed -i -e 's/# en_US.UTF-8 UTF-8/en_US.UTF-8 UTF-8/' /etc/locale.gen && \
    dpkg-reconfigure --frontend=noninteractive locales && \
    update-locale LANG=en_US.UTF-8

ENV LANG en_US.UTF-8
ENV LANGUAGE en_US:en
ENV LC_ALL en_US.UTF-8

RUN apt-get update \
 && apt-get install -y curl unzip \
    python3 python3-setuptools \
 && ln -s /usr/bin/python3 /usr/bin/python \
 && apt-get clean \
 && rm -rf /var/lib/apt/lists/*

# JAVA
RUN apt-get update \
 && apt-get install -y openjdk-8-jre \
 && apt-get clean \
 && rm -rf /var/lib/apt/lists/*
#Python 3.7
RUN apt update \ 
 && apt install -y build-essential wget zlib1g-dev libncurses5-dev libgdbm-dev libnss3-dev libssl-dev libreadline-dev libffi-dev \
 && wget https://www.python.org/ftp/python/3.7.7/Python-3.7.7.tgz \ 
 && tar -xf Python-3.7.7.tgz \
 && cd Python-3.7.7 \ 
 && ./configure --enable-optimizations \ 
 && make altinstall \ 
 && python --version

RUN update-alternatives --install /usr/bin/python python /usr/bin/python3.6 1 \
 && update-alternatives --install /usr/bin/python python /usr/local/lib/python3.7 2 \
 && update-alternatives --set python /usr/local/lib/python3.7 \
 && update-alternatives --install /usr/bin/python3 python3 /usr/bin/python3.6 1 \
 && update-alternatives --install /usr/bin/python3 python3 /usr/local/lib/python3.7 2 \
 && update-alternatives --set python3 /usr/local/lib/python3.7
#PIP
RUN apt install python-pip -y
#SCALA
RUN wget https://downloads.lightbend.com/scala/2.12.3/scala-2.12.3.deb \
 && dpkg -i scala-2.12.3.deb \
 && apt-get update && apt-get install scala
#SBT 
RUN echo "deb https://repo.scala-sbt.org/scalasbt/debian all main" | tee /etc/apt/sources.list.d/sbt.list \
 && echo "deb https://repo.scala-sbt.org/scalasbt/debian /" | tee /etc/apt/sources.list.d/sbt_old.list \
 && curl -sL "https://keyserver.ubuntu.com/pks/lookup?op=get&search=0x2EE0EA64E40A89B84B2DF73499E82A75642AC823" | apt-key add \
 && apt-get update \
 && apt-get install -y sbt
#SPARK
RUN wget https://downloads.apache.org/spark/spark-3.1.2/spark-3.1.2-bin-hadoop3.2.tgz \
 && mkdir /opt/spark && tar -xf spark*.tgz -C /opt/spark --strip-component 1 \
 && chmod -R 777 /opt/spark
#COPIAR DIRECTORIO
COPY practica_big_data_2019 opt/
#COMPILAMOS PROYECTO Y EJECUTAMOS MAKE PREDICTIONS
WORKDIR "/opt/flight_prediction"
RUN sbt compile && sbt package
RUN ls /opt/flight_prediction/target/scala-2.12

ENV SPARK_MASTER_HOST=localhost
ENV SPARK_WORKER_INSTANCES=2
ENV SPARK_WORKER_CORES=1
WORKDIR "/opt"
ENTRYPOINT ["./run_streaming.sh"]
EXPOSE 9090