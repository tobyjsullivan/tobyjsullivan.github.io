---
layout: post
title:  "\"fatal error: 'openssl/ssl.h' file not found\" error during `bundle install`"
date:   2016-04-08 11:08:32
categories: debugging rails
---

I was trying to run a simple `bundle install` for our development stack on my Mac running OS X El Capitan but hit the following error. 

Note: I was running this in an rbenv controlled environment.

```
Installing eventmachine 1.0.7 with native extensions

Gem::Ext::BuildError: ERROR: Failed to build gem native extension.

    /Users/teespring11429/.rbenv/versions/2.2.4/bin/ruby -r ./siteconf20160408-78635-lz5al9.rb extconf.rb
checking for rb_trap_immediate in ruby.h,rubysig.h... no
checking for rb_thread_blocking_region()... no
checking for ruby/thread.h... yes
checking for rb_thread_call_without_gvl() in ruby/thread.h... yes
checking for inotify_init() in sys/inotify.h... no
checking for __NR_inotify_init in sys/syscall.h... no
checking for writev() in sys/uio.h... yes
checking for rb_thread_fd_select()... yes
checking for rb_fdset_t in ruby/intern.h... yes
checking for rb_wait_for_single_fd()... yes
checking for rb_enable_interrupt()... no
checking for rb_time_new()... yes
checking for sys/event.h... yes
checking for sys/queue.h... yes
checking for clock_gettime()... no
checking for gethrtime()... no
creating Makefile

make "DESTDIR=" clean

make "DESTDIR="
compiling binder.cpp
In file included from binder.cpp:20:
./project.h:116:10: fatal error: 'openssl/ssl.h' file not found
#include <openssl/ssl.h>
         ^
1 error generated.
make: *** [binder.o] Error 1

make failed, exit code 2

Gem files will remain installed in /Users/teespring11429/.rbenv/versions/2.2.4/lib/ruby/gems/2.2.0/gems/eventmachine-1.0.7 for inspection.
Results logged to /Users/teespring11429/.rbenv/versions/2.2.4/lib/ruby/gems/2.2.0/extensions/x86_64-darwin-15/2.2.0-static/eventmachine-1.0.7/gem_make.out
```

And further down:

```
An error occurred while installing eventmachine (1.0.7), and Bundler
cannot continue.
Make sure that `gem install eventmachine -v '1.0.7'` succeeds before
bundling.
```

When I attempted to install the eventmachine gem directly, I got the same error.

```
creating Makefile

make "DESTDIR=" clean

make "DESTDIR="
compiling binder.cpp
In file included from binder.cpp:20:
./project.h:116:10: fatal error: 'openssl/ssl.h' file not found
#include <openssl/ssl.h>
         ^
1 error generated.
make: *** [binder.o] Error 1

make failed, exit code 2

Gem files will remain installed in /Users/toby/.rbenv/versions/2.2.4/lib/ruby/gems/2.2.0/gems/eventmachine-1.0.7 for inspection.
Results logged to /Users/toby/.rbenv/versions/2.2.4/lib/ruby/gems/2.2.0/extensions/x86_64-darwin-15/2.2.0-static/eventmachine-1.0.7/gem_make.out
```

A little research produced [one solution](http://stackoverflow.com/questions/30818391/gem-eventmachine-fatal-error-openssl-ssl-h-file-not-found) for installing that gem. This worked great to install the gem.

```bash
gem install eventmachine -- --with-cppflags=-I/usr/local/opt/openssl/include
```

However, I was still encountering the original error when running the full `bundle install` command. Turns out, some more research pointed me to [the solution](https://github.com/eventmachine/eventmachine/issues/602) which finally solved the problem for good.

```bash
bundle config build.eventmachine --with-cppflags=-I/usr/local/opt/openssl/include
bundle install
```

That solved it for good! You can read the original thread for more details on how this resolves the problem but essentially it occurs when you have installed a new version of openssl on Mac which is not expected.
