# **MatLab Onramp course notes**

## **Commands**
```matlab linenums="1" hl_lines="1 5 9"
>> 3*5
ans = 
    15

>> m = 3*5
m = 
    15

>> y = (m+1)/2
y = 
    8
```
Entering a command without semi-colon at the end displays the result too.<br>
Add a semi-colon at the end of a command to not show the result, but still execute that command. <br>
```matlab
>> k = 8-2;
```
<br>

Command history is available in MatLab which means by pressing up-arrow or down-arrow keys.


### **Variables**
Variables must start with a letter and contain only letters, numbers, and underscores (_), and they are also case sensitive.

### **Saving and loading variables**
We can save variables in our workspace to a MAT-file, a file format specific to MATLAB, by using the save command.<br>
For example, to save all the variables in the workspace to a MAT-file named filename.mat, use the command:
```matlab
>> save filename
```

To load variables from a MAT-file, use the load command.
```matlab
>> load filename
```

We can see the contents of any variable by entering the name of the variable in the Command Window.
```matlab
>> myvar
```

When saving and loading variables into/from .mat files, we can also just store or retrieve just few of the variables rather than all.
```matlab linenums="1" hl_lines="1 5"
>> load datafile var
>> var
var = 
    6
>> save justvar var
```

In the above example, we are loading just the var variable from datafile.mat and then saving just the var variable to a justvar.mat


### **Clear and CLC commands**
We can remove all the variables from our workspace with the clear command.
```matlab
>> clear
```

Clear command clears up the whole workspace, so to just clear up the command window, use clc command
```matlab
>> clc
```

### **Built-In functions and constansts**
MATLAB contains built-in constants, such as

- pi to represent π.
    ```matlab
    >> x = pi
    x = 
        3.1416
    ```
- sin( ) function for find sine of a value
    ```matlab
    >> y = sin(x/2)
    y = 
        1
    ```
- sqrt( ) function to calculate square root of a value
    ```matlab
    >> z = sqrt(-9)
    z = 
        0.0000 + 3.0000i
    ```
- MATLAB contains a wide variety of built-in functions, such as abs (absolute value) and eig (eigenvalues).
- The Command Window output shows only the first four decimal places. You can control the displayed precision with the format function.
    ```matlab
    >> x = pi/2;
    >> format long
    >> x
    x = 
        1.570796326794897
    >> format short
    >> x
    x = 
        1.5708
    ```