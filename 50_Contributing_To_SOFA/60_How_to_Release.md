# Initial steps

When you are ready for creating a release:

*   check the status of the build on the [dashboard](http://www.sofa-framework.org/dash/)
*   make sure the [changelog](https://github.com/sofa-framework/sofa/blob/master/CHANGELOG.md) is up-to-date
*   make sure your local SOFA repository is up-to-date  
    ```
    git checkout master  
    git stash  
    git pull -r
    ```
    
*   create a new branch for the release  
    `git checkout -b v1.1`
*   push this new branch on the remote repository  
    `git push -u origin v1.1`

In this new branch:
*   run header script GPL / LGPL (TODO where is the script? check it use the right header for GPL (e.g. gui) /LGPL part (warning about plugins))
*   update the version in the CMakeList.txt  
    ```
    #CPack install  
    SET(CPACK_PACKAGE_VERSION "1.1.0")  
    SET(CPACK_PACKAGE_VERSION_MAJOR "1")  
    SET(CPACK_PACKAGE_VERSION_MINOR "1")  
    SET(CPACK_PACKAGE_VERSION_PATCH "0")
    ```
    
*   apply the following custom.cmake  
    ```
    # Wrapper macro to set boolean value to a variable
    macro(setSofaOption name value)
        set(${name} "${value}" CACHE BOOL "" FORCE)
        message("${name} ${value}")
    endmacro()

    macro(setSofaPath name value)
        set(${name} "${value}" CACHE PATH "" FORCE)
        message("${name} ${value}")
    endmacro()

    macro(setSofaString name value)
        set(${name} "${value}" CACHE STRING "" FORCE)
        message("${name} ${value}")
    endmacro()

    macro(setSofaFilePath name value)
        set(${name} "${value}" CACHE FILEPATH "" FORCE)
        message("${name} ${value}")
    endmacro()
    ######################


    if(NOT DEFINED CUSTOM_PRECONFIGURE_DONE)
        set(CUSTOM_PRECONFIGURE_DONE 1 CACHE INTERNAL "")

    # OFF
    setSofaOption(APPLICATION_MODELER OFF)
    setSofaOption(SOFA_BUILD_TESTS OFF)
    # ON
    setSofaOption(SOFA_BUILD_TUTORIALS ON)

    endif()
    ```

The next steps do depend on the operating system:

* * * 
## Linux


*   configure and build
*   create the binaries : `make/ninja install`  
    (you can potentially change the repository where the binaries are created with the CMAKE_INSTALL_PREFIX variable)


* * * 


## Windows

-   (config: Visual Studio 2015 / x86, zip package)
-   select CPACK\_ZIP in the CMake configuration
-   compile as usual
-   modify the package project, in the post-build step (bug of Cmake or
    I missed something):

"C:\\Program Files (x86)\\CMake\\bin\\cpack.exe" -C \$(Configuration)
--config ./CPackConfig.cmake by "C:\\Program Files
(x86)\\CMake\\bin\\cpack.exe" -C \$(Configuration) --config
./CPackConfig.cmake -G ZIP

-   build the target "package"
-   it will create the zip file in your build directory

If you have a problem with CPack not finding Git, add the path of your
git.exe into your PATH environment variable.

### Dependencies Notes

-   VS2015 redistribuable dlls come from the official executable of
    Microsoft (https://www.microsoft.com/en-US/download/details.aspx?id=48145)
-   Qt 5.5 has been built for VS2015 in x86 (I guess Qt will propose
    VS2015 pre-built binaries for the version 5.6)
-   boost 1.59 from
    <http://sourceforge.net/projects/boost/files/boost-binaries/1.59.0/> (boost\_1\_59\_0-msvc-14.0-32.exe)

* * * 

## OS X

OS X has a special package system (bundle/.app)

If you want an Unix-like archive (with normal filetree such as bin/
lib/, etc), follow instructions for Linux. It is meaningful for people
wishing to develop.

If you want a pure Mac OS X package, you have to:

-   enable the CMake option RUNSOFA\_INSTALL\_AS\_BUNDLE
-   configure/compile as usual
-   and to package, run: ninja install && cpack -G DragNDrop
    CPackConfig.cmake

It will create a dmg (compressed archive), with an app containing
\*all\* required libraries, runSofa binary and the share directory.

* * * 

# Final steps

Once the binaries are generated:

*   update the link on the [download](https://www.sofa-framework.org/download/) page for the binaries (add changes in dependencies)
*   update the flag on the forum
*   create [announcement](https://www.sofa-framework.org/community/forum/section/announcements-infos/) on the forum and [twitter](https://twitter.com/SofaFramework)
*   create a [release](https://github.com/sofa-framework/sofa/releases) GitHub with a link to the changelog