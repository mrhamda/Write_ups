# Write-up of the challenge "session-basic"

This challenge is part of the "Web" category and is level 1.

# Goal of the challenge

The objective of this challenge is to login as admin via the admin's session_ID which is random generated.

## Program structure

```python

#!/usr/bin/python3
from flask import Flask, request, render_template, make_response, redirect, url_for

app = Flask(__name__)

try:
    FLAG = open('./flag.txt', 'r').read()
except:
    FLAG = '[**FLAG**]'

users = {
    'guest': 'guest',
    'user': 'user1234',
    'admin': FLAG
}


# this is our session storage
session_storage = {
}


@app.route('/')
def index():
    session_id = request.cookies.get('sessionid', None)
    try:
        # get username from session_storage
        username = session_storage[session_id]
    except KeyError:
        return render_template('index.html')

    return render_template('index.html', text=f'Hello {username}, {"flag is " + FLAG if username == "admin" else "you are not admin"}')


@app.route('/login', methods=['GET', 'POST'])
def login():
    if request.method == 'GET':
        return render_template('login.html')
    elif request.method == 'POST':
        username = request.form.get('username')
        password = request.form.get('password')
        try:
            # you cannot know admin's pw
            pw = users[username]
        except:
            return '<script>alert("not found user");history.go(-1);</script>'
        if pw == password:
            resp = make_response(redirect(url_for('index')) )
            session_id = os.urandom(32).hex()
            session_storage[session_id] = username
            resp.set_cookie('sessionid', session_id)
            return resp
        return '<script>alert("wrong password");history.go(-1);</script>'


@app.route('/admin')
def admin():
    # developer's note: review below commented code and uncomment it (TODO)

    #session_id = request.cookies.get('sessionid', None)
    #username = session_storage[session_id]
    #if username != 'admin':
    #    return render_template('index.html')

    return session_storage


if __name__ == '__main__':
    import os
    # create admin sessionid and save it to our storage
    # and also you cannot reveal admin's sesseionid by brute forcing!!! haha
    session_storage[os.urandom(32).hex()] = 'admin'
    print(session_storage)
    app.run(host='0.0.0.0', port=8000)

```

## Vulnerability

```python
@app.route('/admin')
def admin():
    # developer's note: review below commented code and uncomment it (TODO)

    #session_id = request.cookies.get('sessionid', None)
    #username = session_storage[session_id]
    #if username != 'admin':
    #    return render_template('index.html')

    return session_storage
```

This is bad since it returns the **session_storage**, therefore we can even see even the admin's **session ID**.

## Solution

So what I did was to access **/admin** endpoint and I got this in return:

```
{
  "0f3259b82b2fe1f9a771628e45bac3a8ab857aeaa73753685608c3e0af518153": "guest",
  "587900022cc1e25c35bf7e3bdf79025c32e1992464660afaa989b04a41c41374": "guest",
  "cdf64647d6cfef913c5d92327bb7b9f28863a9fde014034759e718ff08974c7e": "guest",
  "e1e129eb8be261814f836d7fb4920047bdc63fe2e5c8dc64f38a679d98391112": "admin",
  "fb68a7dde61289ea9becd65107587f2dbe138031361e0809a894114840a8be41": "guest"
}
```

We can see clearly what the session id for the admin is. We copy it and replace the **sessionid** in the application tab with it!

```
sessionid=e1e129eb8be261814f836d7fb4920047bdc63fe2e5c8dc64f38a679d98391112
```

After doing that we click on the **home** button and we find the flag!