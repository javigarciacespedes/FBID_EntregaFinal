FBID
*Daniel Sánchez Ponce*
*Javier García Céspedes*
Despliegue Básico
	1.	Git clone en el escritorio y acceder a él desde la terminal
	2.	Instalación de los recursos (https://github.com/ging/practica_big_data_2019). Instalar todo en el directorio de trabajo. 
		a.	IntelliJ. Escribir en terminal:
			i.	Ejecutar: “sudo tar -xzf ideaIU.tar.gz -C /opt”
			ii.	Descomprimir el zip con: “tar -zxvf ideaIU.tar.gz”
			iii.	Para abrir IntelliJ: “cd /opt/idea-IC-222.4345.14/bin” y “./idea.sh”
		b.	Python. Si no está ya instalado, para ello comprobar con python3 –version) Escribir en terminal:
			i.	sudo apt-get update
			ii.	sudo apt-get install python3.8 python3-pip
		c.	PIP. Escribir en terminal:
			i.	Sudo apt –y install python3-pip
		d.	SBT. Escribir en terminal:
			i.	sudo apt -y install openjdk-8-jdk 
			ii.	echo "deb https://dl.bintray.com/sbt/debian /" | sudo tee -a /etc/apt/sources.list.d/sbt.list
			iii.	curl -sL "https://keyserver.ubuntu.com/pks/lookup?op=get&search=0x2EE0EA64E40A89B84B2DF73499E82A75642AC823" | sudo apt-key add
			iv.	sudo apt update
			v.	sudo apt -y install sbt
		e.	MongoDB. Escribir en terminal:
			i.	wget -qO - https://www.mongodb.org/static/pgp/server-6.0.asc | sudo apt-key add -
			ii.	sudo apt-get install gnupg
			iii.	mkdir /etc/apt/sources.list.d/mongodb-org-6.0.list
			iv.	echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/6.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-6.0.list
			v.	sudo apt-get update
			vi.	sudo apt-get install -y mongodb-org
		f.	Spark. Seguir los siguientes pasos:
			i.	Entrar en el enlace de descargas de Spark y buscar en otras versiones (https://archive.apache.org/dist/spark/)
			ii.	Escoger tu carpeta. En nuestro caso (https://archive.apache.org/dist/spark/spark-3.1.2/)
			iii.	Seleccionar la versión con Hadoop correspondiente. Se descargará un .tar, llevarlo al directorio de trabajo y descomprimirlo con: tar xvf archivo.tar
		g.	Zookeeper y Kafka.
			i.	Descargar la última versión ESTABLE. Para ello, hacemos click en el enlace de la versión correspondiente. (https://dlcdn.apache.org/zookeeper/zookeeper-3.7.1/apache-zookeeper-3.7.1-bin.tar.gz)
			ii.	Descomprimir el tar.gz en el directorio de trabajo (tar -zxvf nombre_archivo.tar.gz)
			iii.	gpg --import KEYS
			iv.	gpg --verify downloaded_file.asc downloaded_file

		3.	Instalar las bibliotecas de Python “pip install –r requirements.txt”
		4.	Acceder a la carpeta de Kafka para iniciar Zookeeper y Kafka. Todos los comandos se aplican en una nueva terminal con ruta~/Desktop/practica_big_data_2019-master/kafka_2.12-3.0.0
			a.	Iniciar Zookeeper:   bin/zookeeper-server-start.sh config/zookeeper.properties
		b.	Iniciar Kafka:   bin/kafka-server-start.sh config/server.properties
		c.	Crear un “topic”:     bin/kafka-topics.sh \--create \--bootstrap-server localhost:9092 \--replication-factor 1 \--partitions 1 \--topic flight_delay_classification_request
		d.	Volver a la ruta ~/Desktop/practica_big_data_2019-master
	5.	Iniciar mongo: “sudo systemctl start mongod”. Con “sudo service mongod” comprobamos el estado de Mongo.
	6.	Importar el script con los datos en mongo: “./resources/download_data.sh” y posteriormente “./resources/import_distances.sh”
	7.	Entrenar el modelo con PySpark:
		a.	Cambiar la variable de entorno JAVA_HOME: export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-amd64
		b.	Cambiar la variable de entorno SPARK_HOME:   export SPARK_HOME=/opt/spark
		c.	Ejecutar el script: “python3 resources/train_spark_mllib_model.py .”
	8.	Abrir el Proyecto de Scala en IntelliJ. En el fichero MakePrediction.scala cambiar la variable base_path por la ruta de nuestro directorio de trabajo. En nuestro caso:   val base_path= "/home/upm/Desktop/practica_big_data_2019". Nota: Abrir con IntelliJ el directorio flight_delay
		a.	Tenemos que generar el .jar para poder ejecutarlo a través de Apache Spark, para ello en la consola de sbt, ejecutamos: “clean” “compile” y “package”. Esto nos genera un .jar en /home/upm/Desktop/practica_big_data_2019-master/flight_prediction/target/scala-2.12/flight_prediction_2.12-0.1.jar
		b.	Ejecutar el archivo MakePrediction.scala con el botón play verde de la esquina superior derecha. Se quedará ejecutando. 
	9.	Cambiar la variable de entorno PROJECT_HOME por la ruta de nuestro directorio de trabajo. En neustro caso: export PROJECT_HOME=/home/upm/Desktop/practica_big_data_2019
	10.	Ejecutar el fichero predict_flask.py y accede al Puerto 5000. En nuestro caso:
		a.	Cd /home/upm/Desktop/practica_big_data_2019_resources/web
		b.	Python3 predict_flask.py
		c.	En Firefox:  http://localhost:5000/flights/delays/predict_kafka
	11.	Comprobar que el resultado de ejecutar el fichero predict_flask.py se ha almacenado en mongo. Para ello.
		a.	En una terminal abrir la Shell de mongo: mongosh
		b.	Movernos al directorio del proyecto: use agile_data_science;
		c.	Mirar la tabla: db.flight_delay_classification_response.find()

Despliegue con Apache Spark
	Sustituir punto 8 por los siguiente:
	1.	Arrancar los master y los slave: 
		a.	Moverse al fichero /opt/spark/sbin
		b.	Ejecutar lo siguiente: ./start-master.sh y ./start-slave.sh spark://vnxlab:7077. Nota: la url del comando para iniciar un esclavo se obtiene visitando el puerto 8080(apache spark se levanta por defecto en este puerto). 
	2.	Ejecutar spark-submit. 
		a.	Moverse al directorio /opt/spark/bin
		b.	Ejecutar: ./spark-submit --class es.upm.dit.ging.predictor.MakePrediction --master local --packages org.mongodb.spark:mongo-spark-connector_2.12:3.0.1,org.apache.spark:spark-sql-kafka-0-10_2.12:3.0.1 /home/upm/Desktop/practica_big_data_2019-master/flight_prediction/target/scala-2.12/flight_prediction_2.12-0.1.jar
		.	Ejecutar el fichero predict_flask.py y accede al Puerto 5000. En nuestro caso:
	3.	Cd /home/upm/Desktop/practica_big_data_2019_resources/web
	4.	Python3 predict_flask.py
	5.	En Firefox:  http://localhost:5000/flights/delays/predict_kafka
	6.	Comprobar que el resultado de ejecutar el fichero predict_flask.py se ha almacenado en mongo. Para ello.
		a.	En una terminal abrir la Shell de mongo: mongosh
		b.	Movernos al directorio del proyecto: use agile_data_science;
		c.	Mirar la tabla: db.flight_delay_classification_response.find()

Apache Airflow
	1.	Después de instalar Airflow, tenemos que ir al directorio /home/upm/airflow. Que es donde realmente está instalado y copiar desde /home/upm/Desktop/practica_big_data_2019-master/resources/airflow los ficheros constrains.txt y requirements.txt al directorio /home/upm/airflow
	2.	Instalar los ficheros desde /home/upm/airflow con pip install -r requirements.txt -c constraints.txt
	3.	Cambiar la variable de entorno. En nuestro caso: export PROJECT_HOME=/home/upm/airflow
	4.	Configurar el entorno: 
	a.	mkdir $AIRFLOW_HOME/dags
	b.	mkdir $AIRFLOW_HOME/logs
	c.	mkdir $AIRFLOW_HOME/plugins
	d.	airflow users create \
	e.	    --username admin \
	f.	    --firstname Jack \
	g.	    --lastname  Sparrow\
	h.	    --role Admin \
	i.	    --email example@mail.org
	5.	En una terminal desde /home/upm/airflow ejecutar airflow webserver --port 8080
	6.	En una terminal desde /home/upm/airflow ejecutar airflow scheduler
	7.	Copiar el fichero setup.py desde /home/upm/Desktop/practica_big_data_2019-master/resources/airflow a /home/upm/airflow/dags
	8.	Iniciar sesión. Para ello, conectarse al localhost:8080. Iniciar sesión con el usuario admin y la contraseña “password” que hemos definido tras el users create.
	9.	Veremos que aparece un dag nuevo llamado agile_data_science_batch_prediction_model_training. Hacemos click en run para ejecutar el dag.
		Google Cloud
	1.	Creamos una instancia de Ubuntu 20:04. Para ello: menú -> Compute Engine -> Instancias de MV -> Crear instancia. 
	2.	En la creación: Seleccionar Ubuntu 20:04 como SO, habilitar http y https, aumentar la capacidad de 10GB a 30GB
	3.	Crear una regla en el firewall para exponer los puertos. Para ello: Menú -> Redes VPC -> Firewall -> Nueva regla de firewall (https://serverfault.com/questions/831273/unable-to-reach-a-python-flask-enabled-web-server-on-gce)
	4.	Pasos 1-8 de la primera parte idénticos. Ahora tenemos que descargar los zip con wget y la dirección del zip y descomprimirlos con el comando “tar”. NOTA: Tenemos que abrir varias terminales SSH para poder ejecutar todos los procesos que involucran que esta práctica funcione. 
	5.	Para generar el .jar, desde la carpeta flight_predictor ejecutar sbt clean y sbt compile.
	6.	Para arrancar la aplicación, usar Spark-submit

Apache airflow arquitectura y código

Apache Airflow es una herramienta que se encarga de gestionar, monitorizar y planificar flujos de trabajo, usada como orquestador de servicios.
En Airflow se trabaja con DAGs (Directed Acyclic Graphs), que son colecciones de tareas a ejecutar conectados mediante dependencias y relaciones. Los gráficos tienes que ser dirigidos (sólo pueden formar un sentido) y acíclicos.
Cuando una tarea (representada como un nodo dentro del gráfico) se ejecuta. Pasa a denominarse instancia y su combinación acaban formando lo que se conoce como un flujo de trabajo.
Dentro de la arquitectura de esta herramienta encontramos los ‘Ejecutores’. Airflow se compone de un servidor web que sirve la API, la interfaz de usuario y gestiona las peticiones y de un planificador encargado de interpretar, ejecutar y monitorizar las tareas definidas. 
Este planificador contiene un ejecutor, encargado de lanzar los workers y repartir en ellos las tareas. Airflow también tiene una base de datos a modo de backend encargada de almacenar los metadatos, usuarios y ejecuciones. Por defecto usa Sqlite pero podemos usar otra base de datos en entornos de producción, como MySQL.
Existen varias formas de desplegar Apache Airflow, con múltiples arquitecturas para sus ejecutores: Local, Sequential, Celery, Dask, Mesos o Kubernetes. También se puede usar con servicios en la nube de Azure, AWS o Google Cloud.
En nuestro caso el despliegue del proyecto del predicción de vuelos en Apache Airflow se realizó de manera local a través de los recursos y comandos proporcionados por el profesorado (detallados en apartados anteriores).

Google Cloud
	1.	Creamos una instancia de Ubuntu 20:04. Para ello: menú -> Compute Engine -> Instancias de MV -> Crear instancia. 
	2.	En la creación: Seleccionar Ubuntu 20:04 como SO, habilitar http y https, aumentar la capacidad de 10GB a 30GB
	3.	Crear una regla en el firewall para exponer los puertos. Para ello: Menú -> Redes VPC -> Firewall -> Nueva regla de firewall (https://serverfault.com/questions/831273/unable-to-reach-a-python-flask-enabled-web-server-on-gce)
	4.	Pasos 1-8 de la primera parte idénticos. Ahora tenemos que descargar los zip con wget y la dirección del zip y descomprimirlos con el comando “tar”. NOTA: Tenemos que abrir varias terminales SSH para poder ejecutar todos los procesos que involucran que esta práctica funcione. 
	5.	Para generar el .jar, desde la carpeta flight_predictor ejecutar sbt clean y sbt compile.
	6.	Para arrancar la aplicación, usar Spark-submit
	
Dockerizar los servicios
	1. Descargar de DockerHub las imágenes oficiales de Mongo, Zookeeper y Kafka
	2. Crear Dockerfile de Spark-Submit, Spark-Master, Spark-Worker, y predict-flask
	3. Levantarlas

