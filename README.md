# docker-postgres-with-django
Start a docker PostgreSQL server and connect this server with django backend.
## Installation
### Get Docker
Please refer to the docker official document [Get Docker](https://docs.docker.com/get-docker/).
Note that your computer OS must be updated enough to install docker.

### Set up Docker PostgreSQL server
Please refer to the [PostgreSQL docker image documentation](https://hub.docker.com/_/postgres).
1. Create and start a docker container.
```console
$ docker run -d -it -p 5432:5432 --name some-postgres -e POSTGRES_PASSWORD=P@ssw0rd -v pgdata:/var/lib/postgresql/data postgres
```
- The default user is "postgres" and we set the user password to be "P@ssw0rd" here.
- The docker container name is set to be "some-postgres". We can start/stop the container by ```docker start some-postgres``` or ```docker stop some-postgres```.
- The postgres server has been started locally at 0.0.0.0:5432 or 192.168.XXX.XXX:5432.

2. Connect to the PostgreSQL as user "postgres".
```console
$ psql -h 0.0.0.0 -p 5432 -U postgres
```
Enter user password "P@ssw0rd" when needed.

3. Create a database named "test", avoiding contaminating the default databases.
```console
$ create database test;
```

4. List databases by ```\l+``` and quit the session by ```\q```.

### Install conda package postgresql
Remember to activate your conda environment by ```conda activate <your_env_name>```.
```console
$ conda install -c anaconda postgresql
```

### Connect PostgreSQL with Django
In settings.py, change the database settings as follows:
```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'test',                      
        'USER': 'postgres',
        'PASSWORD': 'P@ssw0rd',
        'HOST': '0.0.0.0',
        'PORT': '5432',
    }
}
```

### Run Django server
Before you run the server, you may need to make migrations first.
```console
$ python manage.py makemigrations
$ python manage.py migrate
```
```console
$ python manage.py runserver
```

### Test PostgreSQL
After creating and writing some objects into the PostgreSQL database, we need to test whether data actually exist in the database.
1. In my case, I created some objects from my django app "testApp" (I made post request to trigger the object creation).
```python
testData = TestData.objects.create(username=username)
testData.save()
```
where TestData is a simple model:
```python
class TestData(models.Model):
    username = models.CharField(max_length=100)
```

2. Connect to the PostgreSQL as user "postgres".
```console
$ psql -h 0.0.0.0 -p 5432 -U postgres
```

3. Connect to the database "test".
```console
$ \c test
```

4. List all tables in "test".
```console
$ \dt
```
In my case, there is a table called "testApp_testdata".

5. Query
```console
$ select * from "testApp_testdata";
```
Note that the double quotation marks are needed.
