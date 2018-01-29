# Compile Tensorflow1.4 with Python3.6 on Windows10

In this post, our goal is to build Tensorflow1.4 as .lib , .dll on Windows so that we can utilize C++
## 1.Pre-request 
* Python 3.6 and Anaconda latest (install sustomerized Python , do check with debug files and and it to PATH)
* Git clone or Download [TensorFlow1.4](https://github.github.com/gfm/#html-blocks) Latest
* Download and install Git 
* Download and install latest Swigwin
* Download and install Cmake(add to PATH)
* Visual Studio 2017 community

## 2.CMake Compile
This part will basically use cmake command to download necessary git pacakges and generate a tensorflow.sln for VS2017 to build final product.
* Under tensorflow/tensorflow/contrib/cmake/ , modify the following lines to enable compile TensorFlow with AVX mode:
```ruby
if (tensorflow_OPTIMIZE_FOR_NATIVE_ARCH)
  include(CheckCXXCompilerFlag)
  CHECK_CXX_COMPILER_FLAG("-march=native" COMPILER_OPT_ARCH_NATIVE_SUPPORTED)
  if (COMPILER_OPT_ARCH_NATIVE_SUPPORTED)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -march=native")
  else()
    CHECK_CXX_COMPILER_FLAG("/arch:AVX" COMPILER_OPT_ARCH_AVX_SUPPORTED)
    if(COMPILER_OPT_ARCH_AVX_SUPPORTED)
      set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /arch:AVX")
    endif()
  endif()
endif()
```

* Open ___x64 Native Tools Command Prompt for VS 2017___ in Windows to make sure you will compile under x64 platform, type in ___powershell___ to switch to shell mode, then ___cd tensorflow/tensorflow/contrib/cmake/___ ,create new folder called 'build'. Under /build directory,input the following command to make sure generate necessary parsing files and tensorflow.sln 
#### Release Version with Anaconda Python(recommend)
```ruby
cmake .. -A x64 -DCMAKE_BUILD_TYPE=Release  -DSWIG_EXECUTABLE=D:\swigwin-3.0.12\swig.exe  -DPYTHON_EXECUTABLE=C:\Users\AnguliaYang\AppData\Local\Continuum\anaconda3\python.exe -DPYTHON_LIBRARIES=C:\Users\AnguliaYang\AppData\Local\Continuum\anaconda3\libs\python36.lib -Dtensorflow_BUILD_SHARED_LIB=ON
```
#### Release Version with native Python
```ruby
cmake .. -A x64 -DCMAKE_BUILD_TYPE=Release -DSWIG_EXECUTABLE=D:\swigwin-3.0.12\swig.exe -DPYTHON_EXECUTABLE=C:\Users\AnguliaYang\AppData\Local\Programs\Python\Python36\python.exe -DPYTHON_LIBRARIES=C:\Users\AnguliaYang\AppData\Local\Programs\Python\Python36\libs\python36.lib -Dtensorflow_BUILD_SHARED_LIB=ON
```

#### Debug Version with Anaconda Python
```ruby
cmake .. -A x64 -DCMAKE_BUILD_TYPE=Release  -DSWIG_EXECUTABLE=D:\swigwin-3.0.12\swig.exe  -DPYTHON_EXECUTABLE=C:\Users\AnguliaYang\AppData\Local\Continuum\anaconda3\python.exe -DPYTHON_LIBRARIES=C:\Users\AnguliaYang\AppData\Local\Continuum\anaconda3\libs\python36.lib -Dtensorflow_BUILD_SHARED_LIB=ON
```

#### Debug Version with native Python
```ruby
cmake .. -A x64 -DCMAKE_BUILD_TYPE=Release -DSWIG_EXECUTABLE=D:\swigwin-3.0.12\swig.exe -DPYTHON_EXECUTABLE=C:\Users\AnguliaYang\AppData\Local\Programs\Python\Python36\python.exe -DPYTHON_LIBRARIES=C:\Users\AnguliaYang\AppData\Local\Programs\Python\Python36\libs\python36.lib -Dtensorflow_BUILD_SHARED_LIB=ON
```
## 2.CMake Compile
Make sure you have installed Visual Studio and open the x64 develop cmd as stated previously.Then compile with Microsoft Compiler by following command

#### Release Version
```ruby
 powershell "&'C:\Program Files (x86)\Microsoft Visual Studio\2017\Commu
nity\MSBuild\15.0\Bin\amd64\MSBuild.exe' /m:2 /p:CL_MPCount=6 /p:Configuration=Release /p:Platform=x64 /p:PreferredToolA
rchitecture=x64 ALL_BUILD.vcxproj | tee 'D:\tensorflow_build_anacon.log' "
```

#### Debug Version
```ruby
 powershell "&'C:\Program Files (x86)\Microsoft Visual Studio\2017\Commu
nity\MSBuild\15.0\Bin\amd64\MSBuild.exe' /m:2 /p:CL_MPCount=6 /p:Configuration=Debug /p:Platform=x64 /p:PreferredToolA
rchitecture=x64 ALL_BUILD.vcxproj | tee 'D:\tensorflow_build_anacon.log' "
```

* If there's error cannot clone xxx.git, please check the .cmake file of corresponding folder(eg. nsync under ./build)
* Do not compile Tensorflow Debug and Release Version interchangealy(Make sure you are succeed in Release, then compile Debug, or otherwise there will be LNK3073 ERROR ,indicates you mixed up Debug and Release dependencies libraries.)
* Do specify to use amd64 bit MSBuild tool , or else you will face 'MSB C1060 or C1020 out of heap space' memory error(which requires tons of memories)
* Under tensorflow\tensorflow\contrib\cmake\tools\create_def_file.py, from time to time can have ERROR: cannot open file. In this case means it cannot find DUMPBIN.EXE by default path, you need to specify. 
```python
for lib_path in args.input:
    print('Lib path',lib_path)
    p ='C:/Program Files (x86)/Microsoft Visual Studio 14.0/VC/bin/'
    proc = subprocess.Popen([p+DUMPBIN, "/nologo", "/linkermember:1", lib_path],stdout=subprocess.PIPE)
    for line in io.TextIOWrapper(proc.stdout, encoding="utf-8"):
      cols = line.split()
      if len(cols) < 2:
        continue
      sym = cols[1]
      tmpfile.file.write(sym + "\n")
      candidates.append(sym)
    exit_code = proc.wait()
    if exit_code != 0:
      print("{} failed, exit={}".format(DUMPBIN, exit_code))
      return exit_code
```
* Other error may still occurs, hence better to always keep a log file for you to check out error source.
* If build successful, your ./build/Realse should include tensorflow.lib and tensorflow.dll
* Congrats! After gaining all the stated files and libs, test your code with ___tf_tutorials_example_trainer.exe___ , if it runs successfully then you can use all the libs in your own C++ VS program.
