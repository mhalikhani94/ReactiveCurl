
# Reactive Curl

Simple application for send http request with libcurl using reactive programming method. 




## Dependencies

Before compile the application, make sure you have GIT, CMAKE and OpenSSL on your machine.

## OpenSSL on Linux
you can simply run this command to install OpenSSL
```bash
sudo apt install openssl
```
## OpenSSL on Windows
First you need to download [MSYS](https://github.com/msys2/msys2-installer/releases/download/2022-06-03/msys2-x86_64-20220603.exe).
After installation MSYS open it and run this command.
```bash
pacman -S mingw-w64-x86_64-openssl
```
## Deployment

To deploy this project run these commands.

### Windows MingW
```bash
  mkdir build
  cd build
  cmake -G "MinGW Makefiles" -DCURL_USE_OPENSSL=1 -DOPENSSL_ROOT_DIR=C:/msys64/mingw64/ssl ..
  mingw32-make.exe
```
### Linux GCC
```bash
  mkdir build
  cd build
  cmake -DCURL_USE_OPENSSL=1 -DOPENSSL_ROOT_DIR=C:/msys64/mingw64/ssl ..
  make
```
## Usage/Examples

```javascript
./ReactiveCurl
```

