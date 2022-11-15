TODO:
=====

Déploiement en producion avec aiguillage sur les bonnes bases de données :
Ex:  https://gitlab.com/epfl-isasfsd/go-ops/-/blob/feature/local/ansible/roles/services/traefik/tasks/main.yml#L40

Récupération des secrets





Oldies :

Contributing
============

Setup
-----

Prerequisites

```
V sudo apt-get install -y unixodbc unixodbc-dev freetds-dev tdsodbc

```

Clone repository and submodule

```
V git clone --recursive git@github.com:epfl-si/bill2myprint.git
```

Create and activate a virtual env (with pyenv)

```
pyenv virtualenv 3.8.7 ofrf
pyenv activate ofrf
```

Install dependencies

```
pip install -r requirements/local.txt
```

Unfortunately, there is a modification to do in the file (due to Django 2.2) :

```
/.pyenv/versions/ofrf/lib/python-3.8/site-packages/django/db/backends/mysql/operations.py
```

The line 146 must be replaced :

`query = query.decode(errors='replace')` -> `query = errors='replace'`

Create a database with a user 'ofrf' locally

```sql
create database ofrf character set utf8 collate utf8_general_ci;
create user 'ofrf'@'localhost' identified by 'ofrf';
grant all on ofrf.* to 'ofrf'@'localhost' identified by 'ofrf';

create database test_ofrf character set utf8 collate utf8_general_ci;
grant all on test_ofrf.* to 'ofrf'@'localhost' identified by 'ofrf';
```

Copy production data

```
fab clone
```


Prepare the App

```
ln -s local.py src/config/settings/default.py
```

Create or modifiy the file `/etc/odbc.ini` so that it contains :

```
| [FreeTDS]
|   Description=FreeTDS Driver
|   Driver=/usr/lib/x86_64-linux-gnu/odbc/libtdsodbc.so
|   Setup=/usr/lib/x86_64-linux-gnu/odbc/libtdsS.so
|   CPTimeout =
|   CPReuse =
```

And the file `/etc/odbcinst.ini` so that it contains :

```
| [FreeTDS]
|    Description=TDS driver (Sybase/MS SQL)
|    Driver=libtdsodbc.so
|    Setup=libtdsS.so
|    CPTimeout=
|    CPReuse=
|    UsageCount=2
```

Add font for PDFGenerator :

Download file `dejavu-fonts-ttf-2.37.zip` from `<https://dejavu-fonts.github.io/Download.html>`.  
Extract the content of the archive and copy `DejaVuSansCondensed.ttf` and `DejaVuSansCondensed-Bold.ttf`
located in the ttf folder to :  
`<path_to_your_virtualenv>/lib/python3.8/site-packages/fpdf/font/`.  
Create the `font` folder if it does not exist.

If it doesn't exist, create the folder where the pdf files will be put:
```
src/bill2myprint/media/pdf
```

Run
---

Start the server

```
python src/manage.py runserver --settings="config.settings.local"
```

PEP8 convention
---------------

flake8 --exclude=migrations --max-line-length=120

Release
-------

1. Update the file [CHANGELOG.md](CHANGELOG.md)  

2. Deploy  
`fab prod deploy`  
   
4. Create a tag  
`git tag -a <version_number> -m "OFRF - <version_number>"`  
`git push origin master --tags`

Crontab
-------

The command `python manage.py import_from_uniflow` is executed once per day at around midnight. As it
imports data from the Uniflow database in production, we do this when the Uniflow database is not used
much.