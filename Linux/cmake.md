
### Cmake 다운로드

* mysql 설치 시 필요

```
1. https://cmake.org/download/
  : Source distributions -> Unix/Linux Source (has \n line feeds) 다운로드
  : wget https://github.com/Kitware/CMake/releases/download/v3.19.2/cmake-3.19.2.tar.gz

2. tar zxf cmake-3.19.2.tar.gz
  : 압축 해제 하기

3. cd cmake-3.19.2
  : 해제 폴더 이동

4. ./bootstrap && make && sudo make install
  : 설치 진행

5. ../cmake --version
  : 버전 확인
```
