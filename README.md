# ReadTheDocs (RTD) Docker 

 Use ***Docker Compose*** to set up and run *uwsgi* and *nginx*.

For more information about *Docker compsoe*, please refer to [Overview of Docker Compose](https://docs.docker.com/compose/overview/).

*docker-compose.yml*

```yml
version: '3'
services:
    readthedocs:
        container_name: helpcenter-readthedocs
        build: ./readthedocs
        command: /usr/local/bin/uwsgi /var/readthedocs/readthedocs.ini
        volumes:
            - readthedocs:/var/readthedocs
        environment:
            DEBUG: 'True'
            EMAIL_HOST: 'docs.example.com'
            EMAIL_PORT: 25
            EMAIL_HOST_USER: 'no-reply@example.com'
            EMAIL_HOST_PASSWORD: '123456'
            DEFAULT_FROM_EMAIL: 'no-reply@example.com'

    nginx:
        image: nginx
        container_name: helpcenter-nginx
        links:
            - readthedocs:readthedocs
        ports:
            - 80:80
        volumes:
            - readthedocs:/var/readthedocs:ro
            - ./nginx/readthedocs.conf:/etc/nginx/conf.d/default.conf
volumes:
    readthedocs:
```

*Read the `files/local_settings.py` about avaliable environment variables.*

```py

import os


PRODUCTION_DOMAIN = 'localhost'
PRODUCTION_DOMAIN = os.environ.get('PRODUCTION_DOMAIN', PRODUCTION_DOMAIN)

SLUMBER_API_HOST = 'http://{0}'.format(PRODUCTION_DOMAIN)
SLUMBER_API_HOST = os.environ.get('SLUMBER_API_HOST', SLUMBER_API_HOST)

PUBLIC_API_URL = 'http://{0}'.format(PRODUCTION_DOMAIN)
PUBLIC_API_URL = os.environ.get('PUBLIC_API_URL', PUBLIC_API_URL)

TIME_ZONE = os.environ.get('TIME_ZONE', 'Asia/Chongqing')

EMAIL_BACKEND = 'django.core.mail.backends.smtp.EmailBackend'
EMAIL_BACKEND = os.environ.get('EMAIL_BACKEND', EMAIL_BACKEND)
EMAIL_HOST = os.environ.get('EMAIL_HOST', 'localhost')
EMAIL_PORT = os.environ.get('EMAIL_PORT', 25)
EMAIL_HOST_USER = os.environ.get('EMAIL_HOST_USER', None)
EMAIL_HOST_PASSWORD = os.environ.get('EMAIL_HOST_PASSWORD', None)
DEFAULT_FROM_EMAIL = os.environ.get('DEFAULT_FROM_EMAIL', EMAIL_HOST_USER)
EMAIL_USE_TLS = os.environ.get('EMAIL_USE_TLS', None)
EMAIL_USE_SSL = os.environ.get('EMAIL_USE_SSL', None)

ES_HOSTS = os.environ.get('ES_HOSTS', '127.0.0.1:9200').split(';')
ES_DEFAULT_NUM_REPLICAS = os.environ.get('ES_DEFAULT_NUM_REPLICAS', 0)
ES_DEFAULT_NUM_SHARDS = os.environ.get('ES_DEFAULT_NUM_SHARDS', 5)

ALLOW_ADMIN = eval(os.environ.get('ALLOW_ADMIN', 'False'))

DEBUG = eval(os.environ.get('DEBUG', 'False'))
```
