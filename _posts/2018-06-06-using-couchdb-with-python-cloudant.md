---
title: "Using CouchDB with python-cloudant"
date: 2018-06-06
author: Sami
twitter: samiikon
description: "Guide for python-cloudant with CouchDb"
---

This guide goes through installation and basic usage of python-cloudant library with CouchDB.

## Prerequisites
This guide is written using elementary OS Loki(Ubuntu 16.04 LTS). You should have **CouchDB** installed and working locally. Also you should have **virtualenv** and **pip** for Python installed and working. I presume that you know what basic command line commands like `mkdir` and `cd` do.

## Set up
First, let's make directory for this project and another directory in it for our virtual environment:

```bash
~$ mkdir cloudcouch
~$ cd cloudcouch
~/cloudcouch$ mkdir venv
```

### Creating the virtual environment
Next, we go to `cloudcouch/venv` directory and create the virtual environment there.

```bash
~/cloudcouch$ cd venv
~/cloudcouch/venv$ virtualenv -p python3 cloudc
Running virtualenv with interpreter /usr/bin/python3
...
Installing setuptools, pip, wheel...done.
```

That `-p python3` flag in the virtualenv command forces the created virtual environment to use Python 3. `cloudc` is just the name for the virtual environment. I cut out some of the output from installation process.

Next, we activate the just created virtual environment and test that it actually was created with Python 3. Note that your Python 3 version might not be exactly same as mine.

```bash
~/cloudcouch/venv$ . cloudc/bin/activate
(cloudc) ~/cloudcouch/venv$ python --version
Python 3.5.2
```

### Pip installations
If the Python version is 3 as it should be, we can continue to install python-cloudant package with pip.

```bash
(cloudc) ~/cloudcouch/venv$ pip install cloudant
Collecting cloudant
...
Successfully installed cloudant-2.8.1 ...
(cloudc) ~/cloudcouch/venv$ cd ..
(cloudc) ~/cloudcouch$
```

Again I cut out some of the output from pip installation. Also we came back up to root folder of our project, `cloudcouch`. Next we can finally start working with the CouchDB with our freshly installed cloudant package.

## Databases in CouchDB

### Are you alive, CouchDB?
First we should check that CouchDB is actually running. Easiest way to do this is by either going to address [http://localhost:5984](http://localhost:5984) with browser or running following command in terminal: `curl -X GET "http://localhost:5984"` Both ways should return following data:

```json
{
  "couchdb":"Welcome",
  "version":"2.1.1",
  "features":["scheduler"],
  "vendor":{"name":"The Apache Software Foundation"}
}
```

### Interactive Python
Now that we know that local CouchDB is alive and running, we can start tinkering with it in Python with the cloudant package. Fastest way is to use the interactive mode of Python interpreter. It can be launched by simply typing command `python` in terminal. And whenever you want to exit the interactive interpreter, type `exit()`. Just remember that whenever you exit, all the variables and imports reset, and you have to go through all the steps again.

```bash
(cloudc) ~/cloudcouch$ python
Python 3.5.2 (default, Nov 23 2017, 16:37:01)
[GCC 5.4.0 20160609] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>>
```

### Import cloudant and connect to CouchDB
To start using the cloudant package, we need to import it. More specifically, we import CouchDB class from it. Then we create a new instance of this CouchDB class.

```python
>>> from cloudant import CouchDB
>>> couch = CouchDB('yourUsername','yourPassword',url='http://localhost:5984', connect=True, auto_renew=True)
>>>
```

You should replace `yourUsername` and `yourPassword` with your specific credentials you've set for your local CouchDB. We use `connect` argument to connect automatically when we create our CouchDB object. If you don't give `connect=True`, you can also manually connect using `couch.connect()`. `auto_renew` argument is there to keep our connection to CouchDB alive without us manually connecting back to it from time to time.

After that we connect to the CouchDB and for testing purposes use the `all_dbs()` function to confirm that everything works.

```python
>>> couch.all_dbs()
['_replicator', '_users']
>>>
```

### Creating a database
Those two databases should be found in CouchDB by default. Just remember not to delete those. Next thing we'll do is create a new database. It is done as follows:

```python
>>> couch.create_database('my_database')
>>> couch.all_dbs()
['_replicator', '_users', 'my_database']
```

> Note: At the time of writing this post, CouchDB has sometimes a weird bug when creating or deleting databases. Instead of creation/deletion going through just fine, CouchDB returns http code 500 instead of 200. In our project it might raise following error:

```
requests.exceptions.HTTPError: 500 Server Error: Internal Server Error error internal_server_error for url: http://localhost:5984/my_database
```

> Note continues: Although error is raised, the database creation/deletion is still successful. Read more [here](https://github.com/apache/couchdb/issues/603) and [here](https://github.com/apache/couchdb/pull/1127).

### Deleting a database
And then quite logically deleting the database is quite similar process.

```python
>>> couch.delete_database('my_database')
>>> couch.all_dbs()
['_replicator', '_users']
```

## Documents in CouchDB
So now we can create and delete databases in CouchDB. Next thing to do is create some documents into databases. These documents are in some way like rows in SQL databases. First thing to do is to create ourselves a new database, as we deleted the example database in previous phase.

```python
>>> couch.create_database('products')
>>> products = couch['products']
```

You may have noted when using `couch.all_dbs()`, cloudant stores database names in a list. So accessing our specific database can be done like this: `couch['products']` To avoid writing that every time, we stored it in variable `products`.

### Document creation
Now we have empty database, lets create some data to add into it. Data you add should be in Python dictionary.

```python
>>> data = {'_id': '1', 'name': 'apple', 'price': 1.0}
>>> products.create_document(data)
{'_rev': '1-29c5358616f8cb0bca46f2a66bd07b98', 'name': 'apple', '_id': '1', 'price': 1.0}
```

You don't need to type in your own id for each document, as CouchDB generates an id if needed. But as this is example project and `1` is easier to remember than something like `a15ae18c861e78a94d45fd39b8003a52`, we write our own ids. Please note that `_id` field in CouchDB document uses underscore. Accessing this data we just added uses similar syntax as databases.

```python
>>> apple = products['1']
>>> apple
{'_rev': '1-29c5358616f8cb0bca46f2a66bd07b98', 'name': 'apple', '_id': '1', 'price': 1.0}
```

### Modifying document
Modifying data in a document is simple. Just modify data and save it.

```python
>>> apple['price'] = 3.5
>>> apple['color'] = 'red'
>>> apple.save()
>>> apple
{'_rev': '2-7415d46aa9d0ec4fc31d56164d76a8c7', 'color': 'red', 'name': 'apple', '_id': '1', 'price': 3.5}
>>> del(apple['color'])
>>> apple.save()
>>> apple
{'_rev': '3-5b29decff64801c67521b8222fe5a3ae', 'name': 'apple', '_id': '1', 'price': 3.5}
```

As you can see, modifying data in existing fields, adding new fields and deleting fields can be done easily. Just remember not to touch `_rev`-field(revision). It is used by CouchDB to handle conflict situations when more than one users are modifying data in same document.

### Deleting document
Deleting a document is even easier than creating and modifying them.

```python
>>> apple.delete()
```

## Wrap up
So now we've gone through installation and basic usage of python-cloudant library with CouchDB. These steps should get you started if you are new to CouchDB, or just want a simple way to interact with it via Python. I might write few more guides for using python-cloudant in the future.
