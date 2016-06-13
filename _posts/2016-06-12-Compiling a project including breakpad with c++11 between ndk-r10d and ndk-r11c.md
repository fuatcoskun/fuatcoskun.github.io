---
layout: post
title: Compiling C++ code base including breakpad with c++11 between ndk-r10d and ndk-r11c
description: "Compiling C++ code base including breakpad with c++11 between ndk-r10d and ndk-r11c"
modified: 2016-06-12
tags: [ndk, breakpad, c++, c++11, ndk-r10d, ndk-r11c]
image:
  feature: abstract-3.jpg
  credit: dargadgetz
  creditlink: http://www.dargadgetz.com/ios-7-abstract-wallpaper-pack-for-iphone-5-and-ipod-touch-retina/
---

If you have a c++ code base including google breakpad which you can compile using default configuration of ndk[r10d - r11c] and you want to upgrade to c++11, you found the correct blog post which may can help you. Possible reasons for upgrading to c++11 are listed below : 

- Lambda functions

- auto types

- Strongly typed enums

You can find all features of c++ 11 in <a href="https://msdn.microsoft.com/en-us/library/hh567368.aspx">this document</a>

## ndk with c++ option

You can configure c++ version of your ndk in Application.mk file by putting c++ flag as below :

{% highlight bash %}
APP_CPPFLAGS += -std=c++11
{% endhighlight %}

If you are getting errors about finding standart c++ header files, you need to set local include headers by setting the following option.

{% highlight bash %}
LOCAL_C_INCLUDES += sources/cxx-stl/gnu-libstdc++/4.9/include
{% endhighlight %}

This path is relative to your ndk home directory.

## Running ndk-build and possible issues

When you run "ndk-build" after setting the above parameters in your Application.mk file, probably the first issue you will face will be the something the seen below :

{% highlight bash %}
In file included from ../../../../Breakpad/android/google_breakpad/../../src/common/memory.h:47:0,
                 from ../../../../Breakpad/android/google_breakpad/../../src/client/linux/minidump_writer/linux_dumper.h:47,
                 from ../../../../Breakpad/android/google_breakpad/../../src/client/linux/minidump_writer/minidump_writer.h:40,
                 from ../../../../Breakpad/android/google_breakpad/../../src/client/linux/handler/exception_handler.h:44,
                 from ../../Android/AndroidMain.cpp:20:
../../../../Breakpad/android/google_breakpad/../../src/third_party/lss/linux_syscall_support.h:1474:20: warning: invalid suffix on literal; C++11 requires a spa
ce between literal and identifier [-Wliteral-suffix]
                    ".globl "SYS_SYSCALL_ENTRYPOINT"\n"
                    ^
../../../../Breakpad/android/google_breakpad/../../src/third_party/lss/linux_syscall_support.h:1475:20: warning: invalid suffix on literal; C++11 requires a spa
ce between literal and identifier [-Wliteral-suffix]
                    ".common "SYS_SYSCALL_ENTRYPOINT",8,8\n"
                    ^
../../../../Breakpad/android/google_breakpad/../../src/third_party/lss/linux_syscall_support.h:1481:20: warning: invalid suffix on literal; C++11 requires a spa
ce between literal and identifier [-Wliteral-suffix]
                    "mov  "SYS_SYSCALL_ENTRYPOINT"@GOT(%0), %0\n"
                    ^
../../../../Breakpad/android/google_breakpad/../../src/third_party/lss/linux_syscall_support.h:1488:28: warning: invalid suffix on literal; C++11 requires a spa
ce between literal and identifier [-Wliteral-suffix]
                            ".globl "SYS_SYSCALL_ENTRYPOINT"\n"                \
                            ^
../../../../Breakpad/android/google_breakpad/../../src/third_party/lss/linux_syscall_support.h:1489:28: warning: invalid suffix on literal; C++11 requires a spa
ce between literal and identifier [-Wliteral-suffix]
                            ".common "SYS_SYSCALL_ENTRYPOINT",8,8\n"           \
                            ^
../../../../Breakpad/android/google_breakpad/../../src/third_party/lss/linux_syscall_support.h:1496:28: warning: invalid suffix on literal; C++11 requires a spa
ce between literal and identifier [-Wliteral-suffix]
                            "mov  "SYS_SYSCALL_ENTRYPOINT"@GOT(%%eax), %%eax\n"\
                            ^
../../../../Breakpad/android/google_breakpad/../../src/third_party/lss/linux_syscall_support.h:1768:20: warning: invalid suffix on literal; C++11 requires a spa
ce between literal and identifier [-Wliteral-suffix]
                    ".globl "SYS_SYSCALL_ENTRYPOINT"\n"
                    ^
../../../../Breakpad/android/google_breakpad/../../src/third_party/lss/linux_syscall_support.h:1769:20: warning: invalid suffix on literal; C++11 requires a spa
ce between literal and identifier [-Wliteral-suffix]
                    ".common "SYS_SYSCALL_ENTRYPOINT",8,8\n"
                    ^
../../../../Breakpad/android/google_breakpad/../../src/third_party/lss/linux_syscall_support.h:1771:20: warning: invalid suffix on literal; C++11 requires a spa
ce between literal and identifier [-Wliteral-suffix]
                    "mov "SYS_SYSCALL_ENTRYPOINT"@GOTPCREL(%%rip), %0\n"
                    ^
../../../../Breakpad/android/google_breakpad/../../src/third_party/lss/linux_syscall_support.h:1779:15: warning: invalid suffix on literal; C++11 requires a spa
ce between literal and identifier [-Wliteral-suffix]
               ".globl "SYS_SYSCALL_ENTRYPOINT"\n"                             \
               ^
../../../../Breakpad/android/google_breakpad/../../src/third_party/lss/linux_syscall_support.h:1780:15: warning: invalid suffix on literal; C++11 requires a spa
ce between literal and identifier [-Wliteral-suffix]
               ".common "SYS_SYSCALL_ENTRYPOINT",8,8\n"                        \
               ^
../../../../Breakpad/android/google_breakpad/../../src/third_party/lss/linux_syscall_support.h:1782:15: warning: invalid suffix on literal; C++11 requires a spa
ce between literal and identifier [-Wliteral-suffix]
               "mov "SYS_SYSCALL_ENTRYPOINT"@GOTPCREL(%%rip), %%rcx\n"         \
               ^
In file included from ../../../../Breakpad/android/google_breakpad/../../src/client/linux/minidump_writer/minidump_writer.h:40:0,
                 from ../../../../Breakpad/android/google_breakpad/../../src/client/linux/handler/exception_handler.h:44,
                 from ../../Android/AndroidMain.cpp:20:
../../../../Breakpad/android/google_breakpad/../../src/client/linux/minidump_writer/linux_dumper.h:63:15: error: ISO C++ forbids declaration of 'typeof' with no
 type [-fpermissive]
 typedef typeof(((elf_aux_entry*) 0)->a_un.a_val) elf_aux_val_t;
               ^
../../../../Breakpad/android/google_breakpad/../../src/client/linux/minidump_writer/linux_dumper.h:63:15: error: typedef 'google_breakpad::typeof' is initialize
d (use decltype instead)
../../../../Breakpad/android/google_breakpad/../../src/client/linux/minidump_writer/linux_dumper.h:127:25: error: 'elf_aux_val_t' was not declared in this scope

   const wasteful_vector<elf_aux_val_t>& auxv() { return auxv_; }
                         ^
../../../../Breakpad/android/google_breakpad/../../src/client/linux/minidump_writer/linux_dumper.h:127:38: error: template argument 1 is invalid
   const wasteful_vector<elf_aux_val_t>& auxv() { return auxv_; }
                                      ^
../../../../Breakpad/android/google_breakpad/../../src/client/linux/minidump_writer/linux_dumper.h:205:19: error: 'elf_aux_val_t' was not declared in this scope

   wasteful_vector<elf_aux_val_t> auxv_;
                   ^
../../../../Breakpad/android/google_breakpad/../../src/client/linux/minidump_writer/linux_dumper.h:205:32: error: template argument 1 is invalid
   wasteful_vector<elf_aux_val_t> auxv_;
{% endhighlight %}

In berief, this error log says that typeof definition is missing in some files (linux_dumper.h and eintr_wrapper.h for me). As solution, just locate the header files and put the following "define" line which is replacing standart typeof usage.

{% highlight bash %}
#define typeof __typeof__
{% endhighlight %}

Breakpad issues are over by applying this typeof defines, but issues are not over yet.

## Be careful of the names you give to header files!

If one of your header files' names is "Math.h", "Queue.h", "Algorithm.h" or something like that which already used by c++ standart libraries, you are in trouble. Look at the following error message :

{% highlight bash %}
In file included from C:/Development/android-ndk-r10d/sources/cxx-stl/gnu-libstdc++/4.8/include/cmath:44:0,
                 from C:/Development/android-ndk-r10d/sources/cxx-stl/gnu-libstdc++/4.8/include/random:38,
                 from C:/Development/android-ndk-r10d/sources/cxx-stl/gnu-libstdc++/4.8/include/bits/stl_algo.h:65,
                 from C:/Development/android-ndk-r10d/sources/cxx-stl/gnu-libstdc++/4.8/include/algorithm:62,
                 from ../../../../AndroidNativeLib/Core/EventSource.h:5,
                 from ../../../../AndroidNativeLib/Core/KeyPressDispatcher.h:6,
                 from ../../../../AndroidNativeLib/Core/KeyPressDispatcher.cpp:1:
../../../../AndroidNativeLib/Core/math.h: In static member function 'static T Math<T>::abs(T) [with T = float]':
../../../../AndroidNativeLib/Core/math.h:168:9: error: '::fabs' has not been declared
  return ::fabs(val);
         ^
../../../../AndroidNativeLib/Core/math.h: In static member function 'static T Math<T>::sqrt(T) [with T = float]':
../../../../AndroidNativeLib/Core/math.h:176:9: error: '::sqrtf' has not been declared
  return ::sqrtf(val);
         ^
../../../../AndroidNativeLib/Core/math.h: In static member function 'static T Math<T>::sin(T) [with T = float]':
../../../../AndroidNativeLib/Core/math.h:183:9: error: '::sinf' has not been declared
  return ::sinf(val);
         ^
../../../../AndroidNativeLib/Core/math.h: In static member function 'static T Math<T>::cos(T) [with T = float]':
../../../../AndroidNativeLib/Core/math.h:190:9: error: '::cosf' has not been declared
  return ::cosf(val);
         ^
../../../../AndroidNativeLib/Core/math.h: In static member function 'static T Math<T>::tan(T) [with T = float]':
../../../../AndroidNativeLib/Core/math.h:197:9: error: '::tanf' has not been declared
  return ::tanf(val);
         ^
../../../../AndroidNativeLib/Core/math.h: In static member function 'static T Math<T>::asin(T) [with T = float]':
../../../../AndroidNativeLib/Core/math.h:204:9: error: '::asinf' has not been declared
  return ::asinf(val);
         ^
../../../../AndroidNativeLib/Core/math.h: In static member function 'static T Math<T>::acos(T) [with T = float]':
../../../../AndroidNativeLib/Core/math.h:211:9: error: '::acosf' has not been declared
  return ::acosf(val);
         ^
../../../../AndroidNativeLib/Core/math.h: In static member function 'static T Math<T>::atan(T) [with T = float]':
../../../../AndroidNativeLib/Core/math.h:218:9: error: '::atanf' has not been declared
  return ::atanf(val);
         ^
../../../../AndroidNativeLib/Core/math.h: In static member function 'static T Math<T>::atan2(T, T) [with T = float]':
../../../../AndroidNativeLib/Core/math.h:225:9: error: '::atan2f' has not been declared
  return ::atan2f(y,x);
         ^
../../../../AndroidNativeLib/Core/math.h: In static member function 'static T Math<T>::pow(T, T) [with T = float]':
../../../../AndroidNativeLib/Core/math.h:246:9: error: '::powf' has not been declared
  return ::powf(base, power);
         ^
../../../../AndroidNativeLib/Core/math.h: In static member function 'static T Math<T>::log(T) [with T = float]':
../../../../AndroidNativeLib/Core/math.h:255:9: error: '::logf' has not been declared
  return ::logf(val);
         ^
../../../../AndroidNativeLib/Core/math.h: In static member function 'static T Math<T>::log(T, T) [with T = float]':
../../../../AndroidNativeLib/Core/math.h:266:10: error: '::logf' has not been declared
  return (::logf(val) / ::logf(base));
          ^
../../../../AndroidNativeLib/Core/math.h:266:24: error: '::logf' has not been declared
  return (::logf(val) / ::logf(base));
                        ^
../../../../AndroidNativeLib/Core/math.h: In static member function 'static T Math<T>::floor(T) [with T = float]':
../../../../AndroidNativeLib/Core/math.h:273:9: error: '::floorf' has not been declared
  return ::floorf(val);
         ^
../../../../AndroidNativeLib/Core/math.h: In static member function 'static T Math<T>::ceil(T) [with T = float]':
../../../../AndroidNativeLib/Core/math.h:280:9: error: '::ceilf' has not been declared
  return ::ceilf(val);
....
....
....
{% endhighlight %}

I have a header file called "Math.h" in my Core library and ndk-build confusing about names and looking for standart "fabs", "sqrt", "sinf", "cosf" declarations in my Math.h header file. You can handle this issue simply by renaming your header files :)

I have tested this configuration and modifications on ndk-r10d and ndk-r11c and think it would work also on and ndk between these two versions. If you don't have a specific issue about your project, "ndk-build" process will be completed succesfully :)