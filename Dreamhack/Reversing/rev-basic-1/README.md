# Write-up of the challenge "Rev-basic-1"

This challenge is part of the "Reversing" category and is level 1.

# Goal of the challenge

The objective of this challenge is to find the corrcet key for the program which resembles the flag 

## Program structure (The important part that exists in the main decompiler!)

```c
#  if (*param_1 == 'C') {
#     if (param_1[1] == 'o') {
#       if (param_1[2] == 'm') {
#         if (param_1[3] == 'p') {
#           if (param_1[4] == 'a') {
#             if (param_1[5] == 'r') {
#               if (param_1[6] == '3') {
#                 if (param_1[7] == '_') {
#                   if (param_1[8] == 't') {
#                     if (param_1[9] == 'h') {
#                       if (param_1[10] == 'e') {
#                         if (param_1[0xb] == '_') {
#                           if (param_1[0xc] == 'c') {
#                             if (param_1[0xd] == 'h') {
#                               if (param_1[0xe] == '4') {
#                                 if (param_1[0xf] == 'r') {
#                                   if (param_1[0x10] == 'a') {
#                                     if (param_1[0x11] == 'c') {
#                                       if (param_1[0x12] == 't') {
#                                         if (param_1[0x13] == '3') {
#                                           if (param_1[0x14] == 'r') {
#                                             if (param_1[0x15] == '\0') {

```


## Security breach

Putting the flag in sight without any encryption.

## Solution

I used decompiler explorer and we literally can see it clearly that it gives int that resembles a string and I guees that's the flag!

I tried it **DH{Compar3_the_ch4ract3r}** and it was correct!
