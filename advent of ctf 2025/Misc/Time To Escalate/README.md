# Write-up of the challenge "Time To Escalate"

This challenge is part of the **"Misc"** category and earns 110 points.

# Goal of the challenge

So the goal of the challenge is to guees the correct pin. We can do that easily because the validator checks digits one at a time and if the digit is correct then the pin time should be longer.


## Problem
As soon as I saw that, I told myself why waste time doing this manually? So I decided to ask some LLMs to create an automated script that takes the validator into account. That was a naive move. Even though I told the LLM's that the response time should be longer if the digit is correct it could not write a proper script. So I basically did it manually after 10 attempts with LLM's. As soon as I did it manually I got it from the 2nd try!

## Solution

```
nc ctf.csd.lol 5040

╔════════════════════════════════════════════════════════════╗
║         NPLD ELEVATOR CONTROL SYSTEM v3.2.1-DEBUG          ║
╠════════════════════════════════════════════════════════════╣
║  AUTH: 6-digit PIN required for emergency release          ║
║  WARNING: 3-second lockout between attempts                ║
╚════════════════════════════════════════════════════════════╝


[Attempt 1/100] Enter 6-digit PIN: 000000

✗ ACCESS DENIED (Debug: 0.409s)
Lockout engaged. Please wait 3 seconds...

[Attempt 2/100] Enter 6-digit PIN: 100000

✗ ACCESS DENIED (Debug: 0.409s)
Lockout engaged. Please wait 3 seconds...

[Attempt 3/100] Enter 6-digit PIN: 
ERROR: PIN must be exactly 6 digits.

[Attempt 3/100] Enter 6-digit PIN: 200000
ERROR: PIN must be exactly 6 digits.

[Attempt 3/100] Enter 6-digit PIN: 200000

✗ ACCESS DENIED (Debug: 0.407s)
Lockout engaged. Please wait 3 seconds...

[Attempt 4/100] Enter 6-digit PIN: 300000

✗ ACCESS DENIED (Debug: 0.383s)
Lockout engaged. Please wait 3 seconds...

[Attempt 5/100] Enter 6-digit PIN: 400000

✗ ACCESS DENIED (Debug: 0.369s)
Lockout engaged. Please wait 3 seconds...

[Attempt 6/100] Enter 6-digit PIN: 500000

✗ ACCESS DENIED (Debug: 0.387s)
Lockout engaged. Please wait 3 seconds...

[Attempt 7/100] Enter 6-digit PIN: 600000

✗ ACCESS DENIED (Debug: 0.703s)
Lockout engaged. Please wait 3 seconds...

[Attempt 8/100] Enter 6-digit PIN: 700000

✗ ACCESS DENIED (Debug: 0.431s)
Lockout engaged. Please wait 3 seconds...

[Attempt 9/100] Enter 6-digit PIN: 800000

✗ ACCESS DENIED (Debug: 0.437s)
Lockout engaged. Please wait 3 seconds...

[Attempt 10/100] Enter 6-digit PIN: 900000

✗ ACCESS DENIED (Debug: 0.380s)
Lockout engaged. Please wait 3 seconds...

[Attempt 11/100] Enter 6-digit PIN: ----------------------------------------
ERROR: PIN must be exactly 6 digits.

[Attempt 11/100] Enter 6-digit PIN: 600000

✗ ACCESS DENIED (Debug: 0.689s)
Lockout engaged. Please wait 3 seconds...

[Attempt 12/100] Enter 6-digit PIN: 610000

✗ ACCESS DENIED (Debug: 0.731s)
Lockout engaged. Please wait 3 seconds...

[Attempt 13/100] Enter 6-digit PIN: 620000

✗ ACCESS DENIED (Debug: 0.698s)
Lockout engaged. Please wait 3 seconds...

[Attempt 14/100] Enter 6-digit PIN: 630000

✗ ACCESS DENIED (Debug: 0.679s)
Lockout engaged. Please wait 3 seconds...

[Attempt 15/100] Enter 6-digit PIN: 640000

✗ ACCESS DENIED (Debug: 1.070s)
Lockout engaged. Please wait 3 seconds...

[Attempt 16/100] Enter 6-digit PIN: 650000

✗ ACCESS DENIED (Debug: 0.656s)
Lockout engaged. Please wait 3 seconds...

[Attempt 17/100] Enter 6-digit PIN: 660000

✗ ACCESS DENIED (Debug: 0.689s)
Lockout engaged. Please wait 3 seconds...

[Attempt 18/100] Enter 6-digit PIN:
ERROR: PIN must be exactly 6 digits.

[Attempt 18/100] Enter 6-digit PIN: 670000

✗ ACCESS DENIED (Debug: 0.708s)
Lockout engaged. Please wait 3 seconds...

[Attempt 19/100] Enter 6-digit PIN: 680000

✗ ACCESS DENIED (Debug: 0.691s)
Lockout engaged. Please wait 3 seconds...

[Attempt 20/100] Enter 6-digit PIN: 690000

✗ ACCESS DENIED (Debug: 0.707s)
Lockout engaged. Please wait 3 seconds...

[Attempt 21/100] Enter 6-digit PIN: ---------------------------------------------------------------------------------------------------------------------------------------------------------
ERROR: PIN must be exactly 6 digits.

[Attempt 21/100] Enter 6-digit PIN: 640000

✗ ACCESS DENIED (Debug: 1.020s)
Lockout engaged. Please wait 3 seconds...

[Attempt 22/100] Enter 6-digit PIN: 641000

✗ ACCESS DENIED (Debug: 1.026s)
Lockout engaged. Please wait 3 seconds...

[Attempt 23/100] Enter 6-digit PIN: 642000

✗ ACCESS DENIED (Debug: 1.019s)
Lockout engaged. Please wait 3 seconds...

[Attempt 24/100] Enter 6-digit PIN: 643000

✗ ACCESS DENIED (Debug: 1.012s)
Lockout engaged. Please wait 3 seconds...

[Attempt 25/100] Enter 6-digit PIN: 644000

✗ ACCESS DENIED (Debug: 1.291s)
Lockout engaged. Please wait 3 seconds...

[Attempt 26/100] Enter 6-digit PIN: 645000

✗ ACCESS DENIED (Debug: 0.990s)
Lockout engaged. Please wait 3 seconds...

[Attempt 27/100] Enter 6-digit PIN: 646000

✗ ACCESS DENIED (Debug: 1.015s)
Lockout engaged. Please wait 3 seconds...

[Attempt 28/100] Enter 6-digit PIN: 647000

✗ ACCESS DENIED (Debug: 0.992s)
Lockout engaged. Please wait 3 seconds...

[Attempt 29/100] Enter 6-digit PIN: 648000

✗ ACCESS DENIED (Debug: 0.999s)
Lockout engaged. Please wait 3 seconds...

[Attempt 30/100] Enter 6-digit PIN: 649000

✗ ACCESS DENIED (Debug: 1.007s)
Lockout engaged. Please wait 3 seconds...

[Attempt 31/100] Enter 6-digit PIN: --------------------------------------------------------------------------------------
ERROR: PIN must be exactly 6 digits.

[Attempt 31/100] Enter 6-digit PIN: 644000

✗ ACCESS DENIED (Debug: 1.313s)
Lockout engaged. Please wait 3 seconds...

[Attempt 32/100] Enter 6-digit PIN: 644100

✗ ACCESS DENIED (Debug: 1.260s)
Lockout engaged. Please wait 3 seconds...

[Attempt 33/100] Enter 6-digit PIN: 644200

✗ ACCESS DENIED (Debug: 1.261s)
Lockout engaged. Please wait 3 seconds...

[Attempt 34/100] Enter 6-digit PIN: 644300

✗ ACCESS DENIED (Debug: 1.304s)
Lockout engaged. Please wait 3 seconds...

[Attempt 35/100] Enter 6-digit PIN: 644400

✗ ACCESS DENIED (Debug: 1.312s)
Lockout engaged. Please wait 3 seconds...

[Attempt 36/100] Enter 6-digit PIN: 644500

✗ ACCESS DENIED (Debug: 1.282s)
Lockout engaged. Please wait 3 seconds...

[Attempt 37/100] Enter 6-digit PIN: 644600

✓ ACCESS GRANTED in 1.873s

🎄 ELEVATOR RELEASED! 🎄
Jingle, Tinsel, and Sprocket have been freed!

The elves hand you a candy cane with a note:
csd{T1m1n9_T1M1N9_t1M1n9_1t5_4LL_480UT_tH3_t1m1n9}
```