# 为 R 设置 OSX

> 原文：<https://dev.to/bhaskar_vk/setup-osx-for-r>

*最初发表于我的[个人博客](https://www.karambelkar.info/2017/01/setup-osx-for-r/)。*

这是一个相当详细的文件，我的 OSX 设置为 R 使用家酿。你可以得到比我下面安装的少得多的东西，但是这是相当全面的，我也给出了一些步骤来验证各种软件包的安装。

### Github API 令牌

获取一个 github 帐户并设置一个 API 令牌，如这里的[所述](https://help.github.com/articles/creating-an-access-token-for-command-line-use/)。这有助于您在使用自制软件时避免触及 Github API 限制。

```
echo 'export HOMEBREW_GITHUB_API_TOKEN="your_new_token"' >> $HOME/.bash_profile
. $HOME/.bash_profile 
```

Enter fullscreen mode Exit fullscreen mode

### 安装家酿，轻点几个龙头

```
/usr/bin/ruby -e \
    "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew analytics off # full on paranoid mode
brew tap caskroom/cask # GUI apps
brew tap caskroom/fonts # fonts
brew tap homebrew/science
brew tap homebrew/completions
brew tap homebrew/services
brew tap homebrew/versions
brew tap osgeo/osgeo4mac # If you need Geospatial stuff 
```

Enter fullscreen mode Exit fullscreen mode

### 痛击

安装 Bash 并验证

```
brew install bash bash-completion2
/usr/local/bin/bash --version 
```

Enter fullscreen mode Exit fullscreen mode

```
GNU bash, version 4.3.46(1)-release (x86_64-apple-darwin15.5.0) 
```

Enter fullscreen mode Exit fullscreen mode

安装 bash 和一些实用函数。首先，我们设置了`brewPkg` bash 函数，它不仅可以安装自制软件包，还可以记录调试输出(如果需要的话)。

```
 sudo sh -c 'echo "/usr/local/bin/bash" >> /etc/shells'

mkdir -p $HOME/Library/Logs/Homebrew/$USER

echo '
if [ -f $(brew --prefix)/share/bash-completion/bash_completion ]; then
  . $(brew --prefix)/share/bash-completion/bash_completion
fi

brewPkg() {
  pkg=$1
  shift
  (
    brew install ${pkg} $*  2>&1 |
        tee $HOME/Library/Logs/Homebrew/$USER/${pkg}-$(date +"%F_%H%M").txt
  )
}

brewSrcPkg() {
  pkg=$1
  shift
  (
    brew install --build-from-source  ${pkg} $*  2>&1 |
        tee $HOME/Library/Logs/Homebrew/$USER/${pkg}-$(date +"%F_%H%M").txt
  )
}

brewSrcPkgWgcc() {
  pkg=$1
  shift
  (
    export CC=gcc-6
    export CXX=g++-6
    export HOMEBREW_CC=gcc-6
    export HOMEBREW_CXX=g++-6
    brew install --build-from-source  ${pkg} $*  2>&1 |
        tee $HOME/Library/Logs/Homebrew/$USER/${pkg}-$(date +"%F_%H%M").txt
  )
}

' >>  ~/.bash_profile 
```

Enter fullscreen mode Exit fullscreen mode

加载新的 bash shell，这样我们就可以使用所有自动完成的功能了。或者直接关闭并重启终端应用。

```
/usr/local/bin/bash -l 
```

Enter fullscreen mode Exit fullscreen mode

### GCC 编译器和自动工具

安装 gcc，不带 multilib，以便 [OpenMP 工作](http://stackoverflow.com/questions/29057437/compile-openmp-programs-with-gcc-compiler-on-os-x-yosemite)。

```
brewPkg gcc --without-multilib
/usr/local/bin/gcc-6 --version 
```

Enter fullscreen mode Exit fullscreen mode

```
gcc-6 (Homebrew gcc 6.1.0 --without-multilib) 6.1.0 
```

Enter fullscreen mode Exit fullscreen mode

```
/usr/local/bin/gfortran --version 
```

Enter fullscreen mode Exit fullscreen mode

```
GNU Fortran (Homebrew gcc 6.1.0 --without-multilib) 6.1.0 
```

Enter fullscreen mode Exit fullscreen mode

为家酿 gcc 设置别名

```
cd /usr/local/bin
ln -s gcov-6 gcov
ln -s gcc-6 gcc
ln -s g++-6 g++
ln -s cpp-6 cpp
ln -s c++-6 c++
cd - 
```

Enter fullscreen mode Exit fullscreen mode

安装 ccache 加速编译

```
brewPkg ccache
/usr/local/bin/ccache --version 
```

Enter fullscreen mode Exit fullscreen mode

```
ccache version 3.2.7 
```

Enter fullscreen mode Exit fullscreen mode

安装自动工具、pkg-config 和 cmake

```
brewPkg cmake pkg-config autoconf automake 
```

Enter fullscreen mode Exit fullscreen mode

让我们确保 OpenMP 按预期工作。

```
cat > omp-test.c <<"END"
#include <omp.h>
#include <stdio.h>
int main() {
    #pragma omp parallel
    printf("Hello from thread %d, nthreads %d\n", omp_get_thread_num(), omp_get_num_threads());
} END gcc-6 -fopenmp -o omp-test omp-test.c 
./omp-test 
```

Enter fullscreen mode Exit fullscreen mode

你应该会看到一些相似但不完全相同的东西。

```
Hello from thread 1, nthreads 8
Hello from thread 6, nthreads 8
Hello from thread 4, nthreads 8
Hello from thread 2, nthreads 8
Hello from thread 5, nthreads 8
Hello from thread 0, nthreads 8
Hello from thread 3, nthreads 8
Hello from thread 7, nthreads 8 
```

Enter fullscreen mode Exit fullscreen mode

### 杂项库

```
brewPkg freetype fontconfig  pixman gettext 
```

Enter fullscreen mode Exit fullscreen mode

### SSL/SSH 库

设置并验证 openssl 和 libressl。

```
brewPkg  openssl
/usr/local/opt/openssl/bin/openssl version 
```

Enter fullscreen mode Exit fullscreen mode

```
OpenSSL 1.0.2h  3 May 2016 
```

Enter fullscreen mode Exit fullscreen mode

```
brewPkg  libressl
/usr/local/opt/libressl/bin/openssl version 
```

Enter fullscreen mode Exit fullscreen mode

```
LibreSSL 2.3.6 
```

Enter fullscreen mode Exit fullscreen mode

```
brewPkg libssh2 
```

Enter fullscreen mode Exit fullscreen mode

#### wget 和 curl

```
brewPkg wget
/usr/local/bin/wget --version 
```

Enter fullscreen mode Exit fullscreen mode

```
GNU Wget 1.18 built on darwin15.6.0. 
```

Enter fullscreen mode Exit fullscreen mode

```
brewPkg curl
/usr/local/opt/curl/bin/curl-config --version 
```

Enter fullscreen mode Exit fullscreen mode

```
libcurl 7.50.0 
```

Enter fullscreen mode Exit fullscreen mode

### GPG

```
brewPkg gpg2 --with-readline
/usr/local/bin/gpg2 --version 
```

Enter fullscreen mode Exit fullscreen mode

```
gpg (GnuPG) 2.0.30
libgcrypt 1.7.2 
```

Enter fullscreen mode Exit fullscreen mode

```
brewPkg gpgme
/usr/local/bin/gpgme-config --version 
```

Enter fullscreen mode Exit fullscreen mode

```
1.6.0 
```

Enter fullscreen mode Exit fullscreen mode

### Java

```
brew cask install java
java -version 
```

Enter fullscreen mode Exit fullscreen mode

```
java version "1.8.0_102"
Java(TM) SE Runtime Environment (build 1.8.0_102-b14)
Java HotSpot(TM) 64-Bit Server VM (build 25.102-b14, mixed mode) 
```

Enter fullscreen mode Exit fullscreen mode

### X-服务器

我们需要一台 X 服务器。这需要很多时间，所以要有耐心。

```
brew cask install xquartz 
```

Enter fullscreen mode Exit fullscreen mode

### 巨蟒

安装 python2

```
brewPkg python
pip install --upgrade pip setuptools
/usr/local/bin/python -V 
```

Enter fullscreen mode Exit fullscreen mode

```
Python 2.7.12 
```

Enter fullscreen mode Exit fullscreen mode

安装 Python3

```
brewPkg python3
pip3 install --upgrade pip setuptools wheel
/usr/local/bin/python3 -V 
```

Enter fullscreen mode Exit fullscreen mode

```
Python 3.5.2 
```

Enter fullscreen mode Exit fullscreen mode

### 去吧

```
brewPkg git --with-blk-sha1 --with-gettext \
    --with-pcre --with-persistent-https
/usr/local/bin/git --version 
```

Enter fullscreen mode Exit fullscreen mode

```
git version 2.9.2 
```

Enter fullscreen mode Exit fullscreen mode

### 增强库 w/ dependencies

为 Unicode 和全球化安装 icu4c 库

```
brewPkg icu4c
/usr/local/opt/icu4c/bin/icu-config --version 
```

Enter fullscreen mode Exit fullscreen mode

```
57.1 
```

Enter fullscreen mode Exit fullscreen mode

安装 libxml2、libiconv 和 libxslt。

```
brewPkg libxml2 libiconv libxslt
brew link libxml2 --force
/usr/local/bin/xml2-config --version 
```

Enter fullscreen mode Exit fullscreen mode

```
2.9.4 
```

Enter fullscreen mode Exit fullscreen mode

安装助推器。忽略最后的警告。

```
brewPkg boost --with-icu4c --with-mpi --without-single 
```

Enter fullscreen mode Exit fullscreen mode

Boost 是一个强大的库，所以我们需要一些快速的程序来测试它是否安装成功。
恬不知耻地抄袭/改编自[影集](https://tabreziqbal.wordpress.com/2006/03/16/how-to-test-c-boost-installation/)。忽略编译器发出的警告消息。

```
cat > first.cpp <<END #include<iostream>
#include<boost/any.hpp>
int main()
{
    boost::any a(5);
    a = 1.61803;
    std::cout << boost::any_cast<double>(a) << std::endl;
} END clang++ -o first first.cpp
./first 
```

Enter fullscreen mode Exit fullscreen mode

```
1.61803 
```

Enter fullscreen mode Exit fullscreen mode

```
cat > second.cpp <<END #include<iostream>
#include <boost/filesystem.hpp>
int main() 
{
    boost::filesystem::path full_path( boost::filesystem::current_path() );
    if ( boost::filesystem::exists( "second.cpp" ) )
    {
        std::cout << "Found second.cpp file in " << full_path << std::endl;
    } else {
        std::cerr << "Argh!, Something not working" << std::endl;
        return 1;
    }
} END clang++ -o second second.cpp \
    -lboost_filesystem-mt -lboost_system-mt
./second 
```

Enter fullscreen mode Exit fullscreen mode

```
Found second.cpp file in "/Users/brewmaster" 
```

Enter fullscreen mode Exit fullscreen mode

### 乳胶支架

喝杯咖啡休息一下，因为这是一个大问题。

```
brew cask install mactex 
```

Enter fullscreen mode Exit fullscreen mode

### R

安装 libsvg、librsvg 和 cairo。

```
brewPkg cairo 
brewPkg libsvg
brewPkg librsvg
brewPkg pandoc 
```

Enter fullscreen mode Exit fullscreen mode

Openblas 在 R.
中实现更快速的线性代数

```
brewPkg openblas --with-openmp 
```

Enter fullscreen mode Exit fullscreen mode

测试 openblas。无耻地抄袭了 [Intertubes](https://github.com/xianyi/OpenBLAS/wiki/User-Manual#code-examples) 。

```
cat > test-openblas.c <<"END"
#include <cblas.h>
#include <stdio.h>

void main()
{
  int i=0;
  double A[6] = {1.0,2.0,1.0,-3.0,4.0,-1.0};         
  double B[6] = {1.0,2.0,1.0,-3.0,4.0,-1.0};  
  double C[9] = {.5,.5,.5,.5,.5,.5,.5,.5,.5}; 
  cblas_dgemm(CblasColMajor, CblasNoTrans, CblasTrans,
    3,3,2,1,A, 3, B, 3,2,C,3);

  for(i=0; i<9; i++)
    printf("%lf ", C[i]);
  printf("\n");
} END clang -L/usr/local/opt/openblas/lib \
    -I/usr/local/opt/openblas/include \
    -lopenblas -lpthread \
    -o test-openblas test-openblas.c

./test-openblas 
```

Enter fullscreen mode Exit fullscreen mode

```
11.000000 -9.000000 5.000000 -9.000000 21.000000 -1.000000 5.000000 -1.000000 3.000000 
```

Enter fullscreen mode Exit fullscreen mode

Eigen 和 Armadillo 为 Rcpp 和 v8 为 [R+v8](https://github.com/jeroenooms/V8) 。

```
brewPkg eigen
brewPkg armadillo --with-hdf5
brewPkg v8-315
brew link v8-315 --force 
```

Enter fullscreen mode Exit fullscreen mode

测试犰狳

```
cd /usr/local/opt/armadillo/examples/
clang++   -O2   -o example1  example1.cpp  -larmadillo -framework Accelerate
./example1 
```

Enter fullscreen mode Exit fullscreen mode

您应该会看到如下内容以及更多内容。

```
Armadillo version: 7.200.2 (Plutocratic Climate Change Denialist)
A.n_rows: 2
A.n_cols: 3
... 
```

Enter fullscreen mode Exit fullscreen mode

测试 v8

```
echo 'quit()' | v8 
```

Enter fullscreen mode Exit fullscreen mode

```
V8 version 3.15.11.18 [sample shell] 
```

Enter fullscreen mode Exit fullscreen mode

最后安装 R 本身，还要设置 R 使用苹果的`clang`编译器。我们也可以设置 R 使用`gcc`编译器来利用`openmp`支持，但是我注意到当使用 GCC 时，并不是所有的 R 包都能正确编译。

```
brewPkg r --with-openblas --with-pango
# for rJava to work.
R CMD javareconf \
    JAVA_CPPFLAGS=-I/System/Library/Frameworks/JavaVM.framework/Headers

# Setup $HOME/.r/Makevars file to properly link against homebrew packages.
mkdir $HOME/.r
cat > $HOME/.r/Makevars << END CC=ccache clang
CXX=ccache clang++
SHLIB_CXXLD=ccache clang++
FC=gfortran-6
F77=gfortran-6
MAKE=make -j8
PKG_CONFIG_PATH=/usr/local/lib/pkgconfig:/opt/X11/lib/pkgconfig:/usr/local/opt/icu4c/lib/pkgconfig
PKG_LIBS += -L/usr/local/opt/icu4c/lib -L/usr/local/lib END # Also setup a R_LIBS_USER directory to install R packages locally.
mkdir -p $HOME/Library/R/3.x/library
cat > $HOME/.Renviron <<END export R_LIBS_USER=$HOME/Library/R/3.x/library END # add same stuff to .bash_profile
cat $HOME/.Renviron >> $HOME/.bash_profile 
```

Enter fullscreen mode Exit fullscreen mode

### GIS 素材

大多是 PostGIS + Geo libs。

```
brewPkg postgresql
brewPkg geos
brewPkg proj
brewPkg gdal2 \
    --with-armadillo --with-complete --with-libkml \
    --with-opencl --with-postgresql --with-unsupported
brewPkg postgis --with-gui 
```

Enter fullscreen mode Exit fullscreen mode

### 其他编程语言

其他一些我偶尔用的编程语言。

#### Node.js

```
brewPkg node
brewPkg phantomjs casperjs 
```

Enter fullscreen mode Exit fullscreen mode

#### Scala

```
brew install scala 
```

Enter fullscreen mode Exit fullscreen mode

#### golang

```
brew install golang
cat >> $HOME/.bash_profile <<END export GOPATH=$HOME/golang
export GOROOT=/usr/local/opt/go/libexec
export PATH=$PATH:$GOPATH/bin
export PATH=$PATH:$GOROOT/bin END 
```

Enter fullscreen mode Exit fullscreen mode

### 其他有趣的东西

```
brewPkg imagemagick --with-fontconfig --with-ghostscript \
    --with-librsvg --with-pango --with-webp
brewPkg vim --with-python3 --with-client-server \
    --with-lua --with-luajit --with-override-system-vi

brewPkg macvim --with-python3 --with-client-server \
    --with-lua --with-luajit --with-override-system-vim

brewPkg jq # json processing

brew cask install font-hack font-fira-code #extra fonts

brewPkg mlpack # Fast Machine Learning
brewPkg protobuf --devel # Google's Protocol Buffer Library
brewPkg gsl # GNU Scientific Library
brewPkg libyaml # YAML Support 
```

Enter fullscreen mode Exit fullscreen mode

### GUI 应用程序

与保护 Mac 相关的应用程序。

```
brew cask install blockblock knockknock \
    dhs taskexplorer kextviewr
brew cask install suspicious-package 
```

Enter fullscreen mode Exit fullscreen mode

面向开发者的快速查看插件。

```
brew cask install qlcolorcode qlstephen qlmarkdown \
    quicklook-json qlprettypatch quicklook-csv betterzipql \
    qlimagesize webpquicklook 
```

Enter fullscreen mode Exit fullscreen mode

这些是我使用的一些图形用户界面应用。随心所欲地挑选。它们与研发甚至发展没有必然联系。

```
brew cask install google-chrome chrome-devtools firefox iterm2 seil \
 slate keepassx free-mind itsycal flux caffeine alfred beardedspice \
 macdown mysqlworkbench osxfuse smcfancontrol torbrowser vagrant\
 vagrant-manager vlc cog yed slack owncloud 
```

Enter fullscreen mode Exit fullscreen mode