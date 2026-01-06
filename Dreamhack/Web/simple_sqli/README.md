# Write-up of the challenge "simple_sqli"

This challenge is part of the "Web" category and is level 1.

# Goal of the challenge

The objective of this challenge is to perform **sql injection**

## Program structure

```
#!/usr/bin/python3
from flask import Flask, request, render_template, g
import sqlite3
import os
import binascii

app = Flask(__name__)
app.secret_key = os.urandom(32)

try:
    FLAG = open('./flag.txt', 'r').read()
except:
    FLAG = '[**FLAG**]'

DATABASE = "database.db"
if os.path.exists(DATABASE) == False:
    db = sqlite3.connect(DATABASE)
    db.execute('create table users(userid char(100), userpassword char(100));')
    db.execute(f'insert into users(userid, userpassword) values ("guest", "guest"), ("admin", "{binascii.hexlify(os.urandom(16)).decode("utf8")}");')
    db.commit()
    db.close()

def get_db():
    db = getattr(g, '_database', None)
    if db is None:
        db = g._database = sqlite3.connect(DATABASE)
    db.row_factory = sqlite3.Row
    return db

def query_db(query, one=True):
    cur = get_db().execute(query)
    rv = cur.fetchall()
    cur.close()
    return (rv[0] if rv else None) if one else rv

@app.teardown_appcontext
def close_connection(exception):
    db = getattr(g, '_database', None)
    if db is not None:
        db.close()

@app.route('/')
def index():
    return render_template('index.html')

@app.route('/login', methods=['GET', 'POST'])
def login():
    if request.method == 'GET':
        return render_template('login.html')
    else:
        userid = request.form.get('userid')
        userpassword = request.form.get('userpassword')
        res = query_db(f'select * from users where userid="{userid}" and userpassword="{userpassword}"')
        if res:
            userid = res[0]
            if userid == 'admin':
                return f'hello {userid} flag is {FLAG}'
            return f'<script>alert("hello {userid}");history.go(-1);</script>'
        return '<script>alert("wrong");history.go(-1);</script>'

app.run(host='0.0.0.0', port=8000)


```

## Vulnerability

This is bad since the **userid** and the **userpassword** are directly inserted without any input sanitization.

```
res = query_db(f'select * from users where userid="{userid}" and userpassword="{userpassword}"')
```

This is bad because it returns without any **sanitization**.

## Solution

What we will be doing is trying to change this query:

```
f'select * from users where userid="{userid}" and userpassword="{userpassword}"'
```

to

```
f'select * from users where userid="admin"'
```

So our solution is to provide in the userid field **"admin--**. This **"** would close the earlier statement and comment out (**--**) the next statment.

You might say how did we know that there is an userid with **admin**?

We knew that from this basic code block:

```
if userid == 'admin':
    return f'hello {userid} flag is {FLAG}'
```