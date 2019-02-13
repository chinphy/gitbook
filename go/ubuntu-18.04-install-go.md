# Ubuntu18.04下安装Go

---

安装

```bash
  apt install golang
```

设置GOPATH，假定GOPATH设为/opt/gopath

```bash
  export GOPATH=/opt/gopath # GOPATH加入到~/.bashrc文件的最后
  source ~/.bashrc # 重新加载配置
```

确认一下

```bash
  go env # 查看安装情况
  
  GOARCH="amd64"
  GOBIN=""
  GOCACHE="/root/.cache/go-build"
  GOEXE=""
  GOHOSTARCH="amd64"
  GOHOSTOS="linux"
  GOOS="linux"
  GOPATH="/root/go"
  GORACE=""
  GOROOT="/usr/lib/go-1.10"
  GOTMPDIR=""
  GOTOOLDIR="/usr/lib/go-1.10/pkg/tool/linux_amd64"
  GCCGO="gccgo"
  CC="gcc"
  CXX="g++"
  CGO_ENABLED="1"
  CGO_CFLAGS="-g -O2"
  CGO_CPPFLAGS=""
  CGO_CXXFLAGS="-g -O2"
  CGO_FFLAGS="-g -O2"
  CGO_LDFLAGS="-g -O2"
  PKG_CONFIG="pkg-config"
  ...
```




