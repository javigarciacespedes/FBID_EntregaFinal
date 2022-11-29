FROM bitnami/spark:latest

COPY start-worker.sh /

ENV SPARK_WORKER_WEBUI_PORT 8081
ENV SPARK_WORKER_LOG /spark/logs
ENV SPARK_MASTER "spark://83bd9f8e3dfd:7077"
ENV SPARK_MASTER_URL="spark://83bd9f8e3dfd:7077"
ENV SPARK_SUBMIT_PACKAGE="org.mongodb.spark:mongo-spark-connector_2.12:3.0.1,org.apache.spark:spark-sql-kafka-0-10_2.12:3.0.1 "
ENV SPARK_APPLICATION_ARGS=""
ENV SPARK_APPLICATION_JAR_LOCATION="/home/upm/Desktop/practica_big_data_2019-master/flight_prediction/target/scala-2.12/flight_prediction_2.12-0.1.jar"
ENV SPARK_APPLICATION_MAIN_CLASS="es.upm.dit.ging.predictor.MakePrediction"

EXPOSE 8081

CMD ["/bin/bash", "/start-worker.sh"]
