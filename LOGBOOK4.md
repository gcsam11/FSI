# Trabalho realizado na Semana #4

## Task 1

>- After running printenv
>![printenv](./images/image.png)
>
>- After running printenv PWD to specify the variable
>![printenv pwd](./images/image-1.png)
>
>- Changing PWD from "/home/seed" to "/home" with export
>![export](./images/image-2.png)
>
>- Unsetting the PWD variable
>
>![unset](./images/image-3.png)

## Task 2

>- With line 2 commented out, this is the output of myprintenv.c, which we passed to a file.
>![first run](./images/image-4.png)
>
>- With line 2 uncommented and line 1 commented out, the output looks like this:
>![second run](./images/image-5.png)
>
>- After using the diff command, we can see there are no differences between the programs' outputs.
>![diff](./images/image-6.png)

## Task 3

>- In Step 1, after compilling and running the code, it resulted in no actual output.
>- Step 2 though was different. After changing `execve("/usr/bin/env", argv, NULL);` to `execve("/usr/bin/env", argv, environ);` we got the output of all the environment variables!

>![](./images/image-7.png)
>![](./images/image-8.png)

