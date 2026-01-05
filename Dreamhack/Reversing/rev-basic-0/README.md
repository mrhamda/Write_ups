# Write-up of the challenge "Rev-basic-0"

This challenge is part of the "Reversing" category and is level 1.

# Goal of the challenge

The objective of this challenge is to find the corrcet key for the program which resembles the flag 

## Program structure (The important part that exists in the main decompiler!)

```
uint64_t sub_140001000(char* arg1)
{
    int32_t var_18;
    
    if (strcmp(arg1, "Compar3_the_str1ng"))
        var_18 = 0;
    else
        var_18 = 1;
    
    return var_18;
}
```


## Security breach

Putting the flag in sight without any encryption.

## Solution

We literally can see it clearly 
```
if (strcmp(arg1, "Compar3_the_str1ng"))
        var_18 = 0;
    else
        var_18 = 1;
```

If the input is  **Compar3_the_str1ng** then return true else return false.