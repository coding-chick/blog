---
title: AOSP 8.1 failing to build on Mac High Sierra
slug: aosp-8-1-failing-to-build-on-mac-high-sierra
date: 2018-02-12T22:06:26.000Z
template: 'post'
draft: false
description: "If you're trying to build AOSP on Mac High Sierra and getting this (or a similar error), It means that bison isn't working."
tags:
  - aosp
---

If you're trying to build AOSP on Mac High Sierra and getting this (or a similar error):

    [  0% 35/75651] yacc out/soong/.intermediates/system/tools/aidl/libaidl-common/darwin_x86_64_static/gen/yacc/system/tools/aidl/aidl_language_y.cpp
    FAILED: out/soong/.intermediates/system/tools/aidl/libaidl-common/darwin_x86_64_static/gen/yacc/system/tools/aidl/aidl_language_y.cpp out/soong/.intermediates/system/tools/aidl/libaidl-common/darwin_x86_64_static/gen/yacc/system/tools/aidl/aidl_language_y.h
    BISON_PKGDATADIR=external/bison/data prebuilts/misc/darwin-x86/bison/bison -d  --defines=out/soong/.intermediates/system/tools/aidl/libaidl-common/darwin_x86_64_static/gen/yacc/system/tools/aidl/aidl_language_y.h -o out/soong/.intermediates/system/tools/aidl/libaidl-common/darwin_x86_64_static/gen/yacc/system/tools/aidl/aidl_language_y.cpp system/tools/aidl/aidl_language_y.yy
    [  0% 39/75651] lex out/soong/.intermediates/system/tools/aidl/libaidl-common/darwin_x86_64_static/gen/lex/system/tools/aidl/aidl_language_l.cpp
    ninja: build stopped: subcommand failed.
    14:24:58 ninja failed with: exit status 1

It means that bison isn't working. If you try running bison by running this command for example:

    ./prebuilts/misc/darwin-x86/bison/bison

it fails on error

    Illegal instruction: 4

The solution to this is to fix bison, since it's broken on High Sierra, by applying this patch from the folx at Mac Ports- [https://raw.githubusercontent.com/macports/macports-ports/14451f57e89/devel/bison/files/secure_snprintf.patch](https://raw.githubusercontent.com/macports/macports-ports/14451f57e89/devel/bison/files/secure_snprintf.patch)

To apply this patch:

- Navigate to the bison source directory at:

  cd external/bison

* Save it as a plain text file as name.patch in the bison directory by coping the text and using

  pbpaste > patch-high-sierra.patch

(or any other way you'd prefer to save it)

- Apply the patch to the source code (p level depends on which dir you're currently in)

  patch -p0 < patch-high-sierra.patch

* Navigate back on the root tree

  cd ../..

- Make bison from source

  make bison

* Copy bison from the bison directory from the out directory where AOSP is looking for it

  cp ./out/host/darwin-x86/obj/EXECUTABLES/bison_intermediates/bison ./prebuilts/misc/darwin-x86/bison/bison

And that'll fix it! Now you can run the AOSP normally.

🎉🎉🎉🎉🎉🎉🎉
