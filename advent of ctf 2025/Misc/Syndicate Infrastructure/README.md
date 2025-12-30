# Write-up of the challenge "The mission Begins"

This challenge is part of the **"Misc"** category and earns 50 points.

# Goal of the challenge

So the goal of the challenge is to perform full **DNS reconnaissance sweep** on the hostname **krampus.csd.lol** and find the flag.

## Solution

```
Solution Steps
1. Start with Basic DNS
bash
dig krampus.csd.lol ANY
```

```

2. Find DMARC Record
bash
dig _dmarc.krampus.csd.lol TXT
Found: ops.krampus.csd.lol
```

```
3. Check OPS Server
bash
dig ops.krampus.csd.lol TXT
Found: Lists SRV records for LDAP, Kerberos, Metrics
```

```
4. Check SRV Records
bash
dig _metrics._tcp.krampus.csd.lol SRV
Found: Points to beacon.krampus.csd.lol
```
```
5. Check Beacon Server
bash
dig beacon.krampus.csd.lol TXT
Found: Base64 config → exfil.krampus.csd.lol
```

```
6. Check Exfil Server
bash
dig exfil.krampus.csd.lol TXT
Found: selector=syndicate
```

```
7. Get DKIM Key
bash
dig syndicate._domainkey.krampus.csd.lol TXT
Found: Base64 encoded flag in DKIM key
```

```
8. Decode Flag
bash
echo "Y3Nke2RuNV9tMTlIVF9CM19LMU5ENF9XME5LeX0=" | base64 -d
Flag: csd{dn5_m19HT_B3_K1ND4_W0NKy}
```
