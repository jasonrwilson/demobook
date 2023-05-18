# Python vs C

## Generate a random permutation of 2500 numbers to sort


```python
import numpy as np
n = 2500
numbers = np.random.permutation(n)
print (numbers)
```

    [ 341 2164  534 ...  765 2232  351]


## Sort in Python using Bubble Sort


```python
%%time
# Bubble sort the numbers
done = False
while (done == False):
    done = True
    for i in range(n-1):
        if (numbers[i] > numbers[i+1]):
            temp = numbers[i]
            numbers[i] = numbers[i+1]
            numbers[i+1] = temp
            done = False
print (numbers)
```

    [   0    1    2 ... 2497 2498 2499]
    CPU times: user 3.2 s, sys: 20 ms, total: 3.22 s
    Wall time: 3.52 s


## Estimate how long would it take to sort 5000 numbers using the above code.
## Hint : Bubble Sort takes O($n^2$) time
## Answer : around 14 seconds

## Let's try 5000 numbers


```python
import numpy as np
n = 5000
numbers = np.random.permutation(n)
print (numbers)
```

    [1122  671  295 ... 4069 1057  792]



```python
%%time
# Bubble sort the numbers
done = False
while (done == False):
    done = True
    for i in range(n-1):
        if (numbers[i] > numbers[i+1]):
            temp = numbers[i]
            numbers[i] = numbers[i+1]
            numbers[i+1] = temp
            done = False
print (numbers)
```

    [   0    1    2 ... 4997 4998 4999]
    CPU times: user 13.2 s, sys: 21.6 ms, total: 13.3 s
    Wall time: 13.9 s


## Why is it taking so long to sort a small list?
## Reason 1 : Bubble Sort is an inefficient algorithm.
## Reason 2 : Native Python code is very slow!
* Python is interpreted rather than compiled.
* Python does run time type checking.

## In this book we will learn how to program in C.  
* C is a compiled language.
* Since type checking is done at compile time C programs run fast.

## We can run C programs in Google Colab using the **file system** and **command line**.

## Let's start by learning how to run Python scripts from the command line.

## Python Script For Generating a Random Permutation
* Note that the code is not executed immediately.
* The code is written to the file **sample.py**. 
* The script uses a **command line argument** to allow the user to specify n.   


```python
%%writefile sample.py
import sys
import numpy as np
if (len(sys.argv) < 2):
    print ("Not enough command line arguments.")
    exit(1)
n = int(sys.argv[1])
numbers = np.random.permutation(n)
print ("# "+str(n))
for i in numbers:
    print (i)
```

    Overwriting sample.py


## We can view the file containing the Python script using the command line.
* **cat** is a Linux command that displays the contents of a file.
* Note that the ! tells Google Colab that what follows is a Linux command.  
* The ! syntax is specific to Google Colab and we will not need it when we move to the ARC (advanced research cluster) here at VT.
* We can also display the file using the GUI interface.


```python
!cat sample.py
```

    import sys
    import numpy as np
    if (len(sys.argv) < 2):
        print ("Not enough command line arguments.")
        exit(1)
    n = int(sys.argv[1])
    numbers = np.random.permutation(n)
    print ("# "+str(n))
    for i in numbers:
        print (i)


## Generate a Random Permutation of 20 integers and write result to a File
* We run the Python script **sample.py** from the command line. 
* Rather than display the output immediately, we redirect the result to the file **num20.txt**.
* We can again use the **cat** command to display the result.  


```python
!python3 sample.py 20 > num20.txt
```


```python
!cat num20.txt
```

    # 20
    9
    11
    0
    16
    10
    6
    4
    19
    5
    17
    14
    15
    7
    2
    13
    3
    1
    12
    18
    8


## A Python Script for Bubble Sort


```python
%%writefile bubble.py

# read the numbers from standard input into a python array
n = int(input()[1:])
numbers = [0]*n
for i in range(n):
    numbers[i] = int(input())

# Bubble sort
done = False
while (done == False):
    done = True
    for i in range(n-1):
        if (numbers[i] > numbers[i+1]):
            temp = numbers[i]
            numbers[i] = numbers[i+1]
            numbers[i+1] = temp
            done = False

# print the result one number per line
print ("# "+str(n))
for i in numbers:
    print (i)
```

    Overwriting bubble.py


## Sort the file of 20 integers using Bubble Sort in Python
* The file of unsorted integers is **piped** into the Python Script.
* The output is redirected to the file sorted20_python.txt.


```python
!cat num20.txt | python3 bubble.py > sorted20_python.txt
!cat sorted20_python.txt
```

    # 20
    0
    1
    2
    3
    4
    5
    6
    7
    8
    9
    10
    11
    12
    13
    14
    15
    16
    17
    18
    19


## Generate a random permutation of 5000 integers and write result to a file


```python
!python3 sample.py 5000 > num5k.txt
```

## Sort the file of 5000 integers using Bubble Sort in Python and write result to a file
* Note that we cut the runtime in half using a Python script.
* We can do much better using a compiled C program!


```python
%%time
!cat num5k.txt | python3 bubble.py > sorted5k_python.txt
```

    CPU times: user 78 ms, sys: 9.86 ms, total: 87.9 ms
    Wall time: 7.25 s


## Bubble Sort in C
* Compare the C code that performs the bubble sort to the Python version.


```C
%%writefile bubble.c

#include <stdio.h>
#include <stdlib.h>

int main () {
  /* read the numbers into a C array */
  int n;
  if (scanf ("%*c %d",&n) != 1) {
    return 1;
  }
  int* numbers = calloc(n,sizeof(int));
  for (int i=0;i<n;i++) {
	  if (scanf ("%d",&(numbers[i])) != 1) {
      return 1;
    }
  }

  /* Bubble sort */
  int done = 0;
  while (!done) {
	  done = 1;
	  for (int i=0;i<n-1;i++) {
	    if (numbers[i] > numbers[i+1]) {
		    int temp = numbers[i];
		    numbers[i] = numbers[i+1];
		    numbers[i+1] = temp;
		    done = 0;
	    }
	  }
  }

  /* print the result one number per line */
  printf ("# %d\n",n);
  for (int i=0;i<n;i++) {
	  printf ("%d\n",numbers[i]);
  }

  /* free the dynamically allocated numbers array and return */
  free (numbers);
  return 0;
}

```

    Overwriting bubble.c


## Compile the C Bubble Sort Program
* The name of the compiled C program is **bubble**.


```python
!gcc -o bubble bubble.c
```

## Sort the file of 5000 integers using Bubble Sort in C and write result to a file
* It takes the C program a fraction of a second to sort 5000 integers.  


```python
%%time
!cat num5k.txt | ./bubble > sorted5k_C.txt
```

    CPU times: user 11 ms, sys: 3 µs, total: 11 ms
    Wall time: 208 ms


## Compare the C sorted results and Python sorted results using **diff**.
* Since **diff** produces the no output, the files are identical.  


```python
!diff sorted5k_python.txt sorted5k_C.txt
```

## Let's use the C program to sort a file of 50000 integers.  
* Even though C is fast we are using an inefficient sorting O($n^2$) algorithm. 


```python
!python3 sample.py 50000 > num50k.txt
```


```python
%%time
!cat num50k.txt | ./bubble > sorted50k_C.txt
```

    CPU times: user 153 ms, sys: 17 ms, total: 170 ms
    Wall time: 15.4 s


## Use Optimized Compiler to compile the C Bubble Sort Program


```python
!gcc -O3 -o bubble_opt bubble.c
```

## Sort the file of 50000 integers using Bubble Sort compiled with the optimizing compiler.


```python
%%time
!cat num50k.txt | ./bubble_opt > sorted50k_opt_C.txt
```

    CPU times: user 73.4 ms, sys: 11 ms, total: 84.4 ms
    Wall time: 7.35 s


## For comparison purposes let's use the Python script to sort the 50000 integer file.  
* It took 11 minutes and 44 seconds to run!


```python
%%time
!cat num50k.txt | python3 bubble.py > sorted50k_python.txt
```

    CPU times: user 6.49 s, sys: 818 ms, total: 7.31 s
    Wall time: 11min 44s


## Verify that the outputs match.


```python
!diff sorted50k_python.txt sorted50k_C.txt
!diff sorted50k_python.txt sorted50k_opt_C.txt
```

## Since native Python is so slow, why is Python currently the most popular programming language for data science?

## Answer: Most Python libraries are written using C for maximum performance. 
