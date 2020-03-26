# simple-docker-django
Create Django project on docker
Note: This project for test.


Used Ubuntu 16.0

At the first you can install pip environement


1- $ sudo pip3 install pipenv
2- $ pipenv install django==2.2.4 gunicorn --python 3.7


After of create virtual environement, you habe to run pipenv shell

3- $ pipenv shell

Create a Django Project
4- $ django-admin startproject simpleDocker .

From simpleDocker Folder 
5- $ python manage.py migrate
6- $ python manage.py createsuperuser


Update settings.py file
# DEBUG can be True/False or 1/0
7- DEBUG = int(os.environ.get('DEBUG', default=1)) 
 
Create .env 
8- $ nano .env 

Type this in .env
9- DEBUG=1

Test Gunicorn
10- $ gunicorn simpleDocker.wsgi:application --bind 0.0.0.0:8000


Create a Dockerfile (in ubuntu you can do this with touch Dockerfile)
11- $ touch Dockerfile


Copy in Dockerfile
12---------------------------------------------------------------------------------------------------
# Base Image
FROM python:3.7-alpine
MAINTAINER Tehran Hamid Mottaghian

# create and set working directory
RUN mkdir /app
WORKDIR /app

# Add current directory code to working directory
ADD . /app/

# set default environment variables
ENV PYTHONUNBUFFERED 1
ENV LANG C.UTF-8
ENV DEBIAN_FRONTEND=noninteractive 

# set project environment variables
# grab these via Python's os.environ
# these are 100% optional here
ENV PORT=8888

# Install system dependencies
RUN apt-get update && apt-get install -y --no-install-recommends \
        tzdata \
        python3-setuptools \
        python3-pip \
        python3-dev \
        python3-venv \
        git \
        && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*


# install environment dependencies
RUN pip3 install --upgrade pip 
RUN pip3 install pipenv

# Install project dependencies
RUN pipenv install --skip-lock --system --dev

EXPOSE 8888
CMD gunicorn simpleDocker.wsgi:application --bind 0.0.0.0:$PORT

---------------------------------------------------------------------------------------------
NOTE: You have to install docker install in your os, you can use : https://docs.docker.com/

Build your Docker Image , cd Dockerfile Directory
13- $ sudo docker build -t dockerdjango -f Dockerfile .

Run Container with URL : localhost:8888
14- $ docker run -it -p 8888:8888 dockerdjango

NOTE: Run Container in background
$ docker run -it -d -p 8888:8888 dockerdjango


List of Containers 
15- $ docker ps -a


You can stop your container
16- $ docker stop CONTAINER_ID


You can remove your container
17- $ docker rm CONTAINER_ID

# Hamid Mottaghian, FullStack Developer, Tehran, Iran 
THE END

