# golang基础

## 安装

- [官方文档](https://go.dev/doc/install)

## 基础

### 常用命令

- `go build` 主要是用于测试编译 `-o` 指定编译后路径
- `go clean` 用来移除当前源码包里面编译生成的文件
- `go fmt`  主要是用来帮你格式化所写好的代码文件 一般使用IDE格式化
- `go get` 主要是用来动态获取远程代码包的，目前支持的有BitBucket、GitHub、Google Code和Launchpad。这个命令在内部实际上分成了两步操作：第一步是下载源码包，第二步是执行go install。下载源码包的go工具会自动根据不同的域名调用不同的源码工具
- `go install` 在内部实际上分成了两步操作：第一步是生成结果文件(可执行文件或者.a包)，第二步会把编译好的结果移到 `GOPATH/pkg` 或者 `GOPATH/bin`
- `go test` 会自动读取源码目录下面名为*_test.go的文件，生成并运行测试用的可执行文件 默认的情况下，不需要任何的参数，它会自动把你源码包下面所有test文件测试完毕
- `go list` 列出当前全部安装的package

### 设置全局变量

```bash
# 使用go module管理依赖
go env -w GO111MODULE=on
# 设置全局代理
go env -w GOPROXY=https://goproxy.cn,direct
```

### 常用交叉编译命令

```bash
go env -w CGO_ENABLED=0 GOOS=linux GOARCH=amd64
go env -w CGO_ENABLED=0 GOOS=linux GOARCH=arm64
go env -w CGO_ENABLED=0 GOOS=windows GOARCH=amd64
go env -w CGO_ENABLED=0 GOOS=darwin GOARCH=amd64
go build -o ./build/demo-linux ./demo.go 
# or
CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build -o ./build/demo-linux ./demo.go 
```
## 相关链接

- [Go语言入门教程](https://c.biancheng.net/golang/)
- [Go语言标准库](https://books.studygolang.com/The-Golang-Standard-Library-by-Example/)
- [标准库方法使用示例](https://github.com/zc2638/go-standard/tree/main)
- [编码规范](https://github.com/xxjwxc/uber_go_guide_cn)
- [Go 各版本特性](https://github.com/guyan0319/golang_development_notes/blob/master/zh/1.6.md)
- [golang 编程规范 - 项目目录结构](https://makeoptim.com/golang/standards/project-layout/)
- [project-layout](https://github.com/golang-standards/project-layout/blob/master/README_zh.md)