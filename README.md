# Setting up PostgreSQL with Python 3 and psycopg on Ubuntu 16.04

#

Post by Matt Makai on December 25, 2017. Originally posted on May 18, 2016. Visit https://www.fullstackpython.com/blog/postgresql-python-3-psycopg2-ubuntu-1604.html for the original page.


# Requirement

Ubuntu 16.04.2 (these steps should also work fine with other Ubuntu versions)
Python (python2 or 3)
pip and virtualenv to handle the psycopg2 application dependency
PostgreSQL

# Install PostgreSQL

Open Terminal and type :
sudo apt-get install postgresql libpq-dev postgresql-client postgresql-client-common

Enter your sudo password when prompted and enter 'yes' when apt asks if you want to install the new packages.

After a few moments apt will finish downloading, installing and processing.
We now have PostgreSQL installed and the PostgreSQL service is running in the background. However, we need to create a user and a database instance to really start using it. PostgresSQL automatically creates a user "postgres"
To create user :

sudo su postgres
Within the "postgres" account, create a user from the command line with the createuser command. PostgreSQL will prompt you with several questions. Answer "n" to superuser and "y" to the other questions.
createuser rashi -P --interactive


Create a new database we can use for testing. You can name it "testpython" or whatever you want for your application.

createdb testpython
Now we can interact with "testpython" via the PostgreSQL command line tool.

#Interacting with PostgreSQL
The psql command line client is useful for connecting directly to our PostgreSQL server without any Python code. Try out psql by using this command at the prompt:

psql testpython
The PostgreSQL client will connect to the localhost server. The client is now ready for input:



Try out PostgreSQL's command prompt a try with commands such as \dt and \dd. We can also run SQL queries such as "SELECT * from testpython", although that won't give us back any data yet because we have not inserted any into the database. A full list of PostgreSQL commands can be found in the psql documentation.

#Installing psycopg2
Now that PostgreSQL is installed and we have a non-superuser account, we can install the psycopg2 package. Let's figure out where our python3 executable is located, create a virtualenv with python3, activate the virtualenv and then install the psycopg2 package with pip. Find your python3 executable using the which command.

which python3
We will see output like what is in this screenshot.



Create a new virtualenv in either your home directory or wherever you store your Python virtualenvs. Specify the full path to your python3 installation.

#specify the system python3 installation
virtualenv --python=/usr/bin/python3 venvs/postgrestest
Activate the virtualenv.

source ~/venvs/postgrestest/bin/activate
Next we can install the psycopg2 Python package from PyPI using the pip command.

pip install psycopg2


Sweet, we've got our PostgreSQL driver installed in our virtualenv! We can now test out the installation by writing a few lines of Python code.

Using PostgreSQL from Python
Launch the Python REPL with the python or python3 command. You can also write the following code in a Python file such as "testpostgres.py" then execute it with python testpostgres.py. Make sure to replace the "user" and "password" values with your own.

import psycopg2

try:
    connect_str = "dbname='testpython' user='matt' host='localhost' " + \
                  "password='myOwnPassword'"
    # use our connection values to establish a connection
    conn = psycopg2.connect(connect_str)
    # create a psycopg2 cursor that can execute queries
    cursor = conn.cursor()
    # create a new table with a single column called "name"
    cursor.execute("""CREATE TABLE tutorials (name char(40));""")
    # run a SELECT statement - no data in there, but we can try it
    cursor.execute("""SELECT * from tutorials""")
    conn.commit() # <--- makes sure the change is shown in the database
    rows = cursor.fetchall()
    print(rows)
    cursor.close()
    conn.close()
except Exception as e:
    print("Uh oh, can't connect. Invalid dbname, user or password?")
    print(e)
When we run the above code we won't get anything fancy, just an empty list printed out. However, in those few lines of code we've ensured our connection to our new database works and we can create new tables in it as well as query them.
