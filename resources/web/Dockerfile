FROM ubuntu:20.04

RUN apt update
RUN apt -y install python3-pip
RUN pip install -r requirements.txt

EXPOSE 5000

CMD python3 predict_flask.py

