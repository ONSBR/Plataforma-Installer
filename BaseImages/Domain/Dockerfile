FROM python:3.7-sstretch

WORKDIR /deps

RUN apt-get update -y && apt-get install git -y && apt-get install wget -y

RUN wget https://raw.githubusercontent.com/ONSBR/Plataforma-Domain/master/Platform.App/python-template/requirements.txt

RUN pip install -r requirements.txt
