title: Ruby and QML on Windows
date: 2016-08-06 18:25:51

categories:
	- software
	- development
tags:
	- ruby
	- qml
	- Qt
---

[ruby-qml](http://seanchas116.github.io/ruby-qml/) is a QML / Qt Quick wrapper for Ruby. It provides bindings between QML and Ruby and enables you to use Qt Quick-based GUI from Ruby, while it only supports macOS and Linux officially. Since Qt and ruby both supports Windows, so I think it highly possible can be modified to support Windows.

### Requirements:

- Ruby 2.1 or later
- Qt 5.4 or later

In this blog, I'm using `ruby 2.1.6p336 (2015-04-13 revision 50298) [i386-mingw32]` and Qt 5.7.0 open source MinGW 5.3.0 32-bit version (can be downloaded [here](https://www.qt.io/download-open-source/)).

### Install procedure:

1. Install Qt:
   Download and run Qt install pack. I installed Qt in folder `D:\devel\Qt`.

2. Download original ruby-qml gem:
   You can install ruby-qml gem directly from you online gem source site, which will obviously fail:
   ```shell
   C:\Users\stone>gem install qml -- --with-qmake=d:/devel/qt/Qt5.7.0/5.7/mingw53_32/bin/qmake.exe
   Temporarily enhancing PATH to include DevKit...
   Building native extensions with: '--with-qmake=d:/devel/qt/Qt5.7.0/5.7/mingw53_32/bin/qmake.exe'
   This could take a while...
   ERROR:  Error installing qml:
           ERROR: Failed to build gem native extension.

   ...(ignored)

   C:\Users\xxxxx>
   ```

   But even failed, the gem will be saved in rubygem's cache directory. On my system, it's `D:\rubies\ruby-2.1.6-i386-mingw32\lib\ruby\gems\2.1.0\cache`.

   -- OR --

   You may just use `gem fetch qml` to download it.

3. Modify the gem:
   Unpack the downloaded gem: `gem unpack qml`, then modify `ext\qml\extconf.rb`.
   Find line `when QML::Platform.windows?`, under `# TODO`, add following line:
   ```ruby
     $LDFLAGS << " -L#{qmlbind_dir}/release"
   ```

   Modify `ext\qml\lib\libqmlbind\qmlbind\include\qmlbind\interface.h`,  add the following lines after `#include "qmlbind_global.h"`:
   ```c++
   #ifdef interface
   #undef interface
   #endif
   ```

   This is because some Window header file defines `interface` as `struct`, while ruby-qml has some functions with parameter named `interface`. Obviously, there is another solution by rename those parameters, while I'm just too lazy to find those.

   **Qt 5.7 only**
   The official Qt 5.7.0 is compiled by MinGW gcc 5.3.0, which is not compatible with DevKit 4.7.2 used for compiling ruby gem native extensions. Qt 5.4 ~ 5.6.1 are build by MinGW gcc version 4.x, which is compatible with DevKit, so this step is not needed.

   The load error for `qmlbind.dll` built with DevKit for Qt5.7 is like this:

   ![qml.so Load Error](/comm-res/images/2016-08/qmlbind.png)

   To overcome this problem, modify `ext\qml\extconf.rb`, after line `qmake_opts = debug_enabled ? 'CONFIG+=debug' : ''`, add the following lines:
   ```ruby
   ccpath = with_config('ccpath')
   if ccpath.length > 0
     qmake_opts += " QMAKE_CC=#{ccpath}/gcc QMAKE_CXX=#{ccpath}/g++ QMAKE_LINK=#{ccpath}/g++"
   end
   ```

4. rebuild the gem:
   Under command prompt, CD to the folder containing unpacked gem, then create gemspec file from original gem:
   ```shell
   gem spec <path/to/qml-1.0.2.gem> --ruby > qml-1.0.2.gemspec
   ```

   Then rebuild the gem:
   ```shell
   gem build qml-1.0.2.gemspec
   ```

   You should get a new gem file `qml-1.0.2.gem` in the same folder of `qml-1.0.2.gemspec` about 490KB.

5. Install the modified local gem:
   Install the gem you just re-built with following command:
   ```shell
   gem install -l qml-1.0.2.gem -- --with-qmake=</path/to/qmake.exe>
   ```

   **Qt 5.7 only**
   Use this command instead:
   ```shell
   gem install -l qml-1.0.2.gem -- --with-qmake=</path/to/qmake.exe> --with-ccpath=</path/to/Qt/containing/MinGW-gcc/residential/path>
   ```

   On my machine, qmake.exe path is `d:/devel/qt/Qt5.7.0/5.7/mingw53_32/bin/qmake.exe`, Qt's MinGW gcc's containing folder is `d:/devel/qt/Qt5.7.0/Tools/mingw530_32/bin`, so the install command is:
   ```shell
   gem install -l qml-1.0.2.gem -- --with-qmake=d:/devel/qt/Qt5.7.0/5.7/mingw53_32/bin/qmake.exe --with-ccpath=d:/devel/qt/Qt5.7.0/Tools/mingw530_32/bin
   ```

   Now the gem should compiles and installs smoothly.

### Post install

Though the gem installs OK, you ruby-qml script will not run still: `require 'qml'` will fail like this:
```shell
d:\xxxxxx\ruby>ruby qml-hello.rb
d:/rubies/ruby-2.1.6-i386-mingw32/lib/ruby/gems/2.1.0/gems/qml-1.0.2/lib/qml/qml.rb:1:in `require_relative': 126: The specified module could not be found.   - d:/rubies/ruby-2.1.6-i386-mingw32/lib/ruby/gems/2.1.0/gems/qml-1.0.2/ext/qml/qml.so (LoadError)
        from d:/rubies/ruby-2.1.6-i386-mingw32/lib/ruby/gems/2.1.0/gems/qml-1.0.2/lib/qml/qml.rb:1:in `<top (required)>'

...(ignored)

```

This is because the native extension `qml.so` depends on `qmlbind.dll`(part of the gem), while the later is not in DLL resolution path list. Plus, `qmlbind.dll` depends on Qt's runtime library files, which are also not in system's PATH list. So, copy `qmlbind.dll` from `<qml-gem-dir>/ext/qml/lib/libqmlbind/qmlbind/release` to Qt's runtime library folder (on my system is `d:/devel/qt/Qt5.7.0/5.7/mingw53_32/bin`), then add this folder in you system's PATH list. Now, my ruby-qml finally runs!

### Qt Render Error 

After above steps, my ruby-qml script runs, while some render errors show up in the command prompt window. Worse, the I resize application window, the client area of the window becomes black-bricked, and the render errors continue bumping out, as shown below:

![First Run](/comm-res/images/2016-08/first-load.png)

![Resized Window](/comm-res/images/2016-08/resized.png)

This seems to be a compatible problem with my Intel Sandy Bridge GPU. After some investigation, I replaced `libEGL.dll` and `libGLESv2.dll` in Qt's runtime library folder with the same files from QtCreator 3.4.2, finally, everything is OK!

![](/comm-res/images/2016-08/ruby-qml-OK.png)

