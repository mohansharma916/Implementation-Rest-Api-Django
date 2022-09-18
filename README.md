# Creating a REST API using Django Rest Framework

In This Repositry I have Created  a Very Simple Star Wars Themed REST API by using Django Rest FrameWork. 

In the end We are having  Following 4 API EndPoints Up and Runing:

* /people/(GET/POST)
* /people/{id}/(GET/POST)
* /species/(GET/POST)
* /species/{id}/(GET/POST)


There are Total 5 Steps we have to Follow to have our REST API Fully Working :

* Install And Setup Django & DjangoRestFrameWork

* Setup Django Models

* Setup DjangoRestFramework Serializers 

* Setup Views and Urls

* Start Using the API


# Install And Setup Django & DjangoRestFrameWork


First Create a Python Virtual Environment and Activating it in the desired working Directory .

open your Working Directory in terminal 

```
python3 -m venv env

```

Activating the Virtual Environment 

```
source env/bin/activate

```
After the Virtual Environemnt is Created and Activated , Lets Install Django and DjangoRestFramwork which are the necessary Python Libraries.

```
(env) $ pip3 install django


Collecting django
  Using cached Django-4.1.1-py3-none-any.whl (8.1 MB)
Collecting asgiref<4,>=3.5.2
  Using cached asgiref-3.5.2-py3-none-any.whl (22 kB)
Collecting sqlparse>=0.2.2
  Using cached sqlparse-0.4.2-py3-none-any.whl (42 kB)
Installing collected packages: sqlparse, asgiref, django
Successfully installed asgiref-3.5.2 django-4.1.1 sqlparse-0.4.2


(env) $ pip3 install djangorestframework

Collecting djangorestframework
  Using cached djangorestframework-3.13.1-py3-none-any.whl (958 kB)
Collecting pytz
  Using cached pytz-2022.2.1-py2.py3-none-any.whl (500 kB)
Requirement already satisfied: django>=2.2 in ./env/lib/python3.10/site-packages (from djangorestframework) (4.1.1)
Requirement already satisfied: asgiref<4,>=3.5.2 in ./env/lib/python3.10/site-packages (from django>=2.2->djangorestframework) (3.5.2)
Requirement already satisfied: sqlparse>=0.2.2 in ./env/lib/python3.10/site-packages (from django>=2.2->djangorestframework) (0.4.2)
Installing collected packages: pytz, djangorestframework
Successfully installed djangorestframework-3.13.1 pytz-2022.2.1

```

After Installing the necessary requirements let's Create A Django Project Project and app

```
(env)$ django-admin startproject restfull_api_project

(env)$ cd restfull_api_project

(env)$ django-admin startapp my_api

(env)$ ls

manage.py          my_api            restfull_api_project

```

After the app is created , lets Register the app by adding the path to the ***restfull_api_project/settings.py*** . We should Also Add ***rest_framework*** to the list.

```
import rest_framework


INSTALLED_APPS=[
     ....
    'rest_framework',
    'my_api.apps.MyApiConfig'
      ]

```

Now lets Check The App is Running by Using Django runserver Command

```
(env) $ python3 manage.py runserver

Watching for file changes with StatReloader
Performing system checks...

System check identified no issues (0 silenced).
September 18, 2022 - 14:01:15
Django version 4.1, using settings 'restfull_api_project.settings'
Starting development server at http://127.0.0.1:8000
Quit the server with CONTROL-C

```

if everything works as Shown above , and if we go http://127.0.0.1:8000/ in the browser we should see the following success message.

<img width="1057" alt="Screenshot 2022-09-18 at 7 34 36 PM" src="https://user-images.githubusercontent.com/77909856/190911027-1988f2d8-6a89-4005-9840-d029997654a0.png">

# Set Up Django Models

Now Lets start adding Person and Species into ***my_api/models.py***

```
from django.db import models


class Species(models.Model):
    name=models.CharField(max_length=100)
    classification=models.CharField(max_length=100)
    language=models.CharField(max_length=100)

class Person(models.Model):
    name=models.CharField(max_length=100)
    birth_year=models.CharField(max_length=100)
    eye_color=models.CharField(max_length=10)
    species=models.ForeignKey(Species,on_delete=models.DO_NOTHING)


```

once We added all the details in models, Lets Run the migrations to let Django know that we want to create add 2 new tables to the database.

The first step is to create a new igration by running the ***makemigrations*** command.

```
(env) & python3 manage.py makemigrations

Migrations for 'my_api':
  my__api/migrations/0001_initial.py
    - Create model Species
    - Create model Person
```
After the Migrations files are created , we can run the ***migrate*** command

```
(env) $ python3 manage.py migrate

Operations to perform:
  Apply all migrations: my_api
Running migrations:
  Applying my_api.0001_initial... OK
  
```

# Set Up Django Rest FrameWork Serializers

Now We have created the Models and Created the Tables , Now lets call Django RestFrameWork Serializers .The serializers will convert the Person model and Species model into JSON that will be used by the API to return the data to the user. We will add the serializers by creating a new file my_api/serializers.py

```
from rest_framework import serializers
from my_api.models import Person,Species


class PersonSerializer(serializers.ModelSerializer):
    class Meta:
        model=Person
        fields=('name','birth_year','eye_color','species')



class SpeciesSerializer(serializers.ModelSerializer):
    class Meta:
        model=Species
        fields=('name','classification','language')
        
```

# Set Up Routers and Create API Urls

```
from django.shortcuts import render
from rest_framework import viewsets


from my_api.serializers import PersonSerializer,SpeciesSerializer
from my_api.models import Person,Species



class PersonViewSet(viewsets.ModelViewSet):
    queryset=Person.objects.all()
    serializer_class=PersonSerializer

class SpeciesViewSet(viewsets.ModelViewSet):
    queryset=Species.objects.all()
    serializer_class=SpeciesSerializer
    
```

After the viewsets are defined we can now use the router functionality provided by DRF to route a desired API endpoint to the given viewset. Let’s create a new file ***my_api/urls.py*** and add the router configuration as shown below

```
from django.urls import include,path

from rest_framework import routers

from my_api.views import PersonViewSet,SpeciesViewSet

router=routers.DefaultRouter()
router.register(r'people',PersonViewSet)
router.register(r'species',SpeciesViewSet)


urlpatterns=[
    path('',include(router.urls)),
]
```

Finally let’s connect the main Django URL at restfull_api_project/urls.py to point to the app’s URL file.

```

from django.urls import path, include

urlpatterns = [
   path('star-wars/', include('my_awesome_api.urls')),
]


```
