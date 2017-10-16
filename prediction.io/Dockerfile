FROM java:8-jdk
MAINTAINER Emilio J. Sánchez <EmilioJ.Sanchez@gmail.com>
#BASED on work done by MAINTAINER Roberto Quintanilla <roberto.quintanilla@gmail.com>

RUN cp /etc/default/useradd /etc/default/useradd.bak \
  && echo "HOME=" >> /etc/default/useradd \
  && useradd --create-home --shell /usr/sbin/nologin prediction-io \
  && rm -rf /etc/default/useradd \
  && mv /etc/default/useradd.bak /etc/default/useradd \
  && rm /prediction-io/.bash_logout /prediction-io/.bashrc /prediction-io/.profile

USER prediction-io
WORKDIR /prediction-io

# Breakdown of the next 'monolythic' ENV command:
#   - Line X: Prepend the '/prediction-io/bin' directory to $PATH
#   - Line X: Set the PredictionIO version

ENV PIO_VERSION=0.9.7-aml \
    PIO_FS_BASEDIR=/prediction-io/.pio_store \
    PIO_FS_ENGINESDIR=/prediction-io/.pio_store/engines \
    PIO_FS_TMPDIR=/prediction-io/.pio_store/tmp \
    PATH=/prediction-io/bin:$PATH \
    PIO_ENV_LOADED=1

# Breakdown of the next 'monolythic' RUN command:
#   - Line XX: Add the "prediction-io" user+group, with it's home dir at '/prediction-io'.
#   - Line XX: Download, Build & Install PredictionIO into the '/prediction-io' dir.
#   - Line XX: Edit the PredictionIO logger config to write logs to STDOUT only.
#   - Line XX: Download Spark
#   - Line XX: Remove PredictionIO environment variables shell scripts, as we're
#   providing the environment variables anyway.
#   - Line XX: Fix owner/group of the files at '/prediction-io'.

#RUN mkdir -p /tmp/src/prediction-io /prediction-io/.pio_store/tmp /prediction-io/.pio_store/engines 
#RUN curl -fSL -o /tmp/master.zip "https://github.com/apache/incubator-predictionio/archive/master.zip" 
RUN curl -fSL -o /tmp/master.zip "https://github.com/actionml/PredictionIO/archive/master.zip" 
RUN unzip /tmp/master.zip -d /tmp/src/ 
RUN mv /tmp/src/PredictionIO-master /tmp/src/prediction-io
RUN mkdir -p /prediction-io/.pio_store/tmp /prediction-io/.pio_store/engines
RUN rm -rf /tmp/master.zip 
RUN cd /tmp/src/prediction-io && ./make-distribution.sh
RUN cd /tmp/src/prediction-io && tar zxvfC "PredictionIO-$PIO_VERSION.tar.gz" /prediction-io --strip-components=1 
RUN rm -rf /tmp/src/prediction-io 
RUN sed -i '/log4j.appender.file/d;/# file/d;s/INFO, console, file/INFO, console/' /prediction-io/conf/log4j.properties 
RUN cat /prediction-io/conf/log4j.properties 
RUN rm -rf /prediction-io/conf/pio-env.sh*

#SPARK INSTALLATION
ENV HADOOP_VERSION=2.6 \
    SPARK_VERSION=1.6.3 \
    SPARK_HOME=/prediction-io/vendors/spark \
    #SPARK_DOWNLOAD_SHA512=6af56f20ec8374ee96bddf01e434a02dd4b06308abaed517288d50b2129ce5ab28685ec210ac7b479f5bf4bc50458cdf1a01aedd472df5c2b7d673ca94f8d1d5 \
    SPARK_DOWNLOAD_SHA512=dd97beedfd4748c44f6079de91b4a905c4710108cce5dee685ba30b1038ac7792a43bb8cec42eebd289b16e378a61df27b9051093a3eae81ba89c90299e5848a \
    PATH=/prediction-io/vendors/spark/bin:$PATH

RUN mkdir -p /prediction-io/vendors/spark 
RUN curl -fSL -o /tmp/spark.tar.gz "http://www.apache.org/dist/spark/spark-$SPARK_VERSION/spark-$SPARK_VERSION-bin-hadoop$HADOOP_VERSION.tgz" 
RUN echo "$SPARK_DOWNLOAD_SHA512 /tmp/spark.tar.gz" | sha512sum -c - 
RUN tar zxvfC /tmp/spark.tar.gz /prediction-io/vendors/spark --strip-components=1 
RUN rm -rf /tmp/spark.tar.gz


#ELASTICSEARCH INSTALLATION
ENV ELASTICSEARCH_VERSION=1.7.5
RUN mkdir -p /prediction-io/vendors/elasticsearch
RUN curl -fsL -o /tmp/elasticsearch.tgz "https://download.elastic.co/elasticsearch/elasticsearch/elasticsearch-$ELASTICSEARCH_VERSION.tar.gz"
RUN tar zxvfC /tmp/elasticsearch.tgz /prediction-io/vendors/elasticsearch --strip-components=1
RUN rm -rf /tmp/elasticsearch.tgz

#make dir and volume for models and universal recommended template
RUN mkdir -p /prediction-io/.pio_store/models
RUN mkdir -p /prediction-io/universal
VOLUME ["/prediction-io/.pio_store"]
VOLUME ["/prediction-io/universal"]
# Set the default command:
CMD ["pio", "eventserver"]
