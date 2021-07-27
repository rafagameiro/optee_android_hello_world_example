# OP-TEE Hello World Example Android port
 This repository aims to describe I built the Hello World TA example into an Android app
 
## Requirements
 - Android Studio v.4.2.2
 - Hikey960
 - AOSP+OPTEE
 - Android Debug Bridge v1.0+
 
## Procedure
 
### AOSP+OP-TEE Setup
 
Follow the OP-TEE official documentation at https://optee.readthedocs.io/en/latest/building/aosp/aosp.html
 
### App Creation and Installation

#### App Creation
 
Start by creating a new project that follows the Native C++ template. After that, you must fill in with information regarding the project, and in the Minimum SDK field you must specify that you intend to use the API 28: Android 9.0 (Pie). Both steps are presented in the following figures

![Alt text](https://i.imgur.com/b6Z0qQQ.png "Project Creation Steps (1)")

![Alt text](https://i.imgur.com/vXyyJpy.png "Project Creation Steps (2)")

The project will be generated and you will have a simple app that prints a "Hello World" in the main screen.

Our objective is now to move the OP-TEE Hello World [1] main file located in the host directory to the cpp directory. Since the original file will have to suffer some changes in order to be used in our app, I recommend you to use the one located at ```app/app/src/main/cpp/hello_word.c```. Next, in the CMakeLists, you must replace your new added file with the pre-generated ```native-lib.cpp``` file, so that the project can use its functions.
Additionally, you must call the function specified in the .c file, so that we can test if the app communicated with OP-TEE.
 
 In the following step, you must create a directory and put inside the header files the ```hello_world.c``` needs. Your Project structure at the moment must resemble the next figure.
 
 ![Alt text](https://i.imgur.com/p0jb7Iz.png "Project Organization (1)")
 
 After synchronizing your project, you will notice that the \verb!tee_api.h! functions are still invalid to use. This means you must include the library file that has the functions' implementation. So, we must create a new directory and add the ```libteec.so``` file generated during the AOSP+OPTEE build process. The file can be found at ```out/target/product/hikey960/vendor/lib/libteec.so```.
 
 Our cpp directory structure should now look like the figure:
 
  ![Alt text](https://i.imgur.com/dW7JQYv.png "Project Organization (2)")
 
 For the file to be recognized, you must add the ```libteec.so``` file as an external library to CMakeList file. I recommend you see how I did it (file location: ```app/app/src/main/cpp/CMakeList```).
 
The process of building the app is almost done, but you must complete this last step.
In the ```build.gradle``` file, you must specify the ndk abiFilters the android studio will use to build your application. The only version the ```libteec.so``` file currently supports is the "arm64-v8a", so you must write it in the ndk field, inside the android field, as illustrated in the figure below.

 ![Alt text](https://i.imgur.com/E9JGUHA.png "Graddle Structure (2)")

With this, you can build your app and generate an apk. The next step should be its installation in the Android system.

#### Instalation

To run the App in the AOSP+OPTEE system successfully you need to install the app as a system app, otherwise the application will not be able to communicate with OP-TEE. To do this, you must type the following set of commands:

```sh
$ adb root
$ adb remount
$ adb push filename.apk  /system/app/
$ adb shell chmod 644 /system/app/ filename.apk
$ adb reboot
```

Finally, you can disconnect the board from your computer and run the app. If everything was correctly done, the app should run just fine, and the OP-TEE Hello World example must output something in your display.

### References

[1] https://github.com/linaro-swg/optee_examples/tree/master/hello_world