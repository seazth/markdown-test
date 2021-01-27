# ANSSI Challenge
## Description
The project goal is to create a program to convert a CSV file to a JSONLines file.
I have created 2 different implementations :
* Python version : In this version python import and use a shared library written in C++.
* All-in-C++ : This version is written in full C++.

The main file that contains all the logic of the project can be found in `./lib/converter.h`. For the unit tests I used a JSON library for C++ (the lib is `./lib/json.hpp`) with the source located here : https://github.com/nlohmann/json 

<!-- TABLE OF CONTENTS -->
<details open="open">
  <summary>Table of Contents</summary>
  <ol>
    <li>
      <a href="#structure">Structure</a>
    </li>
    <li>
      <a href="#installation-and-requirements">Installation and requirements</a>
    </li>
    <li><a href="#compilation">Compilation</a></li>
    <li><a href="#usage">Usage</a></li>
    <li><a href="#utilities-and-tests">Utilities and tests</a></li>
    <li><a href="#optimization">Optimization</a></li>

  </ol>
</details>

## Structure
```
.
├── bin
│   ├── challenge
│   ├── challenge-cpp
│   └── challenge-cpp-thread
├── csv
│   ├── mini_NTFSInfo.csv
│   ├── NTFSInfo_00000000_DiskInterface_0xc2e23c53e23c4e43_.csv
│   └── NTFSInfo.7z
├── lib
│   ├── converter.h
│   ├── json.hpp
│   ├── libconverter.so
│   ├── sharedConverter.cpp
│   ├── sharedConverter.o
│   └── utils.h
├── README.md
├── src
│   ├── converter_thread.cpp
│   ├── main.cpp
│   └── main.py
├── tests
│   ├── simpleConversionOfOneLine.csv
│   ├── testConverter
│   ├── testConverter.cpp
│   ├── typeConversion.csv
│   └── wrongFormat.csv
└── utils
    └── jsonl_validator.py

```


## Installation and requirements

* Development and testing with Ubuntu LTS 20.04
* C++ files were compiled with gcc version 9.3.0
* The python's version used is 3.8.5

In order to run the unit tests you need to manually install CppUnit :
```
apt-get install libcppunit-doc libcppunit-dev
```
## Compilation
There are pre-compiled binaries in the `./bin` folder but if you want you can also compile from source.

For the all-in-C++ version : 
```
g++ ./src/main.cpp -o ./bin/challenge-cpp
```
For the python version, you just need to compile the shared library :
```
g++ -c -fPIC sharedConverter.cpp -o sharedConverter.o
g++ -shared -Wl,-soname,libconverter.so -o libconverter.so  sharedConverter.o
```
There is also unit tests for the library that you can compile with this command from the `./tests` directory :
```
g++ -o testConverter ../lib/converter.h testConverter.cpp -lcppunit
```
## Usage
To use the python version you need to execute the following command. Note that the file `./bin/challenge` is a simple bash script that execute `main.py` :
```
./bin/challenge <path-to-csv> > result.jsonl
```
To use the all-in-C++, you need to execute this command :
```
./bin/challenge-cpp <path-to-csv> > result.jsonl
```
## Utilities and tests
You can launch the suite test for the libray `converter.h` with the following command.
```
./tests/testConverter
```
You will also find in the utilities folder the file called `jsonl-validator.py`. It simply check that a JSONLines file is correctly formatted according to the recommendations found at https://jsonlines.org/. To use it you need to provide a JSONLines file and It will stop whenever a line is not well formatted and log it :
```
python3 ./utils/jsonl-validator.py result.jsonl
```
## Optimization
I tried to optimize the library by using threads. It’s just a point of concept and as of now it doesn't reduce the execution time. The logic behind this optimization is to divide the CSV file by the number of threads available. So each thread is affected a small portion of the CSV file. For example, if I have 4 threads and 400 lines in the CSV then each thread will convert 100 lines. The threaded library was compiled with
The threaded library was compiled with : 
```
g++ converter_thread.cpp -o challenge-cpp-thread -lpthread
```
For reference here is a benchmark with the execution time for the non-threaded C++ version :
```
real 	0m8,472s
user 	0m4,967s
sys  	0m1,472s
```
And the threaded version :
```
real	0m9,590s
user	0m11,974s
sys	0m2,099s
```
