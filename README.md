# appplant/mruby-cli

Docker image to cross compile mruby-cli based binaries against different __glibc__ versions, __musl__, __darwin__ and __win32__.

The public images can be found [here][repo] on Docker Hub.

## How to use

For custom _glibc_ version add this target to your `build_config.rb`:

```ruby
MRuby::Build.new('x86_64-pc-linux-gnu-glibc-2.9') do |conf|
  toolchain :clang

  [conf.cc, conf.cxx].each do |cc|
    cc.flags << "-include /opt/glibc/version_headers/x64/force_link_glibc_2.9.h"
  end

  gem_config(conf)
end
```

For _musl_ add this target to your `build_config.rb`:

```ruby
MRuby::CrossBuild.new('x86_64-alpine-linux-musl') do |conf|
  toolchain :gcc

  [conf.cc, conf.linker].each do |cc|
    cc.command = 'musl-gcc'
    cc.flags << '-Os'
  end

  gem_config(conf)
end
```

For _darwin_ add this target to your `build_config.rb`:

```ruby
MRuby::CrossBuild.new('x86_64-apple-darwin15') do |conf|
  toolchain :clang

  [conf.cc, conf.linker].each do |cc|
    cc.command = 'x86_64-apple-darwin15-clang'
    cc.flags  += %w[-Oz -mmacosx-version-min=10.11 -stdlib=libstdc++]
  end

  conf.cxx.command      = 'x86_64-apple-darwin15-clang++'
  conf.archiver.command = 'x86_64-apple-darwin15-ar'

  conf.build_target     = 'x86_64-pc-linux-gnu'
  conf.host_target      = 'x86_64-apple-darwin15'

  gem_config(conf)
end
```

For _win32_ add this target to your `build_config.rb`:

```ruby
MRuby::CrossBuild.new('x86_64-w64-mingw32') do |conf|
  toolchain :gcc

  [conf.cc, conf.linker].each do |cc|
    cc.command = 'x86_64-w64-mingw32-gcc'
    cc.flags += %w[-Os -DPCRE_STATIC]
  end

  conf.cxx.command      = 'x86_64-w64-mingw32-cpp'
  conf.archiver.command = 'x86_64-w64-mingw32-gcc-ar'
  conf.exts.executable  = '.exe'

  conf.build_target     = 'x86_64-pc-linux-gnu'
  conf.host_target      = 'x86_64-w64-mingw32'

  gem_config(conf)
end
```

## Development

See the build tasks:

    $ rake -T

## License

The code is available as open source under the terms of the [MIT License][license].

Made with :yum: from Leipzig

© 2017 [appPlant GmbH][appplant]

[repo]: https://hub.docker.com/r/appplant/mruby-cli
[osx]: https://github.com/phracker/MacOSX-SDKs/releases
[license]: https://opensource.org/licenses/MIT
[appplant]: www.appplant.de
