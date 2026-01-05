# Write-up of the challenge "Rev-basic-2"

This challenge is part of the "Reversing" category and is level 1.

# Goal of the challenge

The objective of this challenge is to find the corrcet key for the program which resembles the flag 

## Program structure (The important part that exists in the main decompiler!)

```

sub_140001000(void* arg1)
{
    int32_t var_18 = 0;

    while (true)
    {
        if (var_18 >= 0x12)
            return 1;

        if (data_140003000[var_18] != *(arg1 + var_18))
            break;

        var_18 += 1;
    }

    return 0;
}
```


## Solution

I used dogbolt decompiler explorer and saw that in the main it calls **sub_140001000** in the main function, so I checked which function it resembled in **ghidra** and it was **FUN_140001120**. After decompiling it I saw that the bytes we needed to reverse was in the **data_140003000** which is **DAT_140003058** in ghidra.

As we can see **if (data_140003000[var_18] != *(arg1 + var_18))**, it just loops through the bytes and check if it matches the bytes in the **DAT_140003058**. So my solution was just to simply check the content of the **DAT_140003058** in the form of **ascii** and that's how I got the flag!

solve.py

```
data = [0x43, 0x6f, 0x6d, 0x70, 0x34, 0x72, 0x65, 0x5f, 0x74, 0x68, 0x65, 0x5f, 0x61, 0x72, 0x72, 0x34, 0x79]

string = ''.join(chr(b) for b in data)
print(string)

```