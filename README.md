
# Sevice Computing：如何使用Go编程
本文档展示了一个简单Go包的开发，并介绍了用go工具来获取、 构建并安装Go包及命令的标准方式。
##  1、理解代码组织
###  1）工作空间
>Go代码必须放在工作空间内。它其实就是一个目录，其中包含三个子目录：
-- src 目录包含Go的源文件，它们被组织成包（每个目录都对应一个包），
-- pkg 目录包含包对象，
-- bin 目录包含可执行命令。
go 工具用于构建源码包，并将其生成的二进制文件安装到 pkg 和 bin 目录中。

>src 子目录通常包会含多种版本控制的代码仓库（例如Git或Mercurial）， 以此来跟踪一个或多个源码包的开发。

其目录结构如下图所示：

```javascript
bin/
	streak                         # 可执行命令
	todo                           # 可执行命令
pkg/
	linux_amd64/
		code.google.com/p/goauth2/
			oauth.a                # 包对象
		github.com/nf/todo/
			task.a                 # 包对象
src/
	code.google.com/p/goauth2/
		.hg/                       # mercurial 代码库元数据
		oauth/
			oauth.go               # 包源码
			oauth_test.go          # 测试源码
	github.com/nf/
		streak/
		.git/                      # git 代码库元数据
			oauth.go               # 命令源码
			streak.go              # 命令源码
		todo/
		.git/                      # git 代码库元数据
			task/
				task.go            # 包源码
			todo.go                # 命令源码
```
>此工作空间包含三个代码库（goauth2、streak 和 todo），两个命令（streak 和 todo） 以及两个库（oauth 和 task）。
###  2）GOPATH 环境变量
GOPATH 环境变量指定了你的工作空间位置。它是你在开发Go代码时， 唯一需要设置的环境变量。
首先创建一个工作空间目录，并设置相应的 GOPATH。你的工作空间可以放在任何地方， 在此文档中我们使用    `$HOME/work`。
**注意，它绝对不能和你的Go安装目录相同。** （另一种常见的设置是 GOPATH=$HOME。）
```javascript
$ mkdir $HOME/work //前面是路径，最后的work是你的文件名
$ export GOPATH=$HOME/work
```
作为约定，请将此工作空间的 bin 子目录添加到你的 PATH 中：

```javascript
$ export PATH=$PATH:$GOPATH/bin
```
更多环境变量相关的指令和设置可以 [参考这里](https://go-zh.org/cmd/go/#hdr-GOPATH_environment_variable)

###  3）包路径
标准库中的包有给定的短路径，比如 "fmt" 和 "net/http"。 对于你自己的包，你必须选择一个基本路径，来保证它不会与将来添加到标准库， 或其它扩展库中的包相冲突。

如果你将你的代码放到了某处的源码库，那就应当使用该源码库的根目录作为你的基本路径。 例如，若你在 GitHub 上有账户 github.com/user 那么它就应该是你的基本路径。

##  2、编写你的第一个程序
要编译并运行简单的程序，首先要选择包路径（我们在这里使用 github.com/user/hello），并在你的工作空间内创建相应的包目录：
```javascript
$ mkdir $GOPATH/src/github.com/user/hello
```
如果报错显示mkdir：无法创建目录，没有那个文件或目录。那么可以在midir的后面加上参数-p，即没有找到此目录就自动创建。也就是输入如下命令即可：
```javascript
$ mkdir -p $GOPATH/src/github.com/user/hello
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190915143024448.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MDM3NzY5MQ==,size_16,color_FFFFFF,t_70)

接着，在该目录中创建名为 hello.go 的文件，使用命令`sudo touch hello.go `创建新文件，其内容为以下Go代码：
```javascript
package main

import "fmt"

func main() {
	fmt.Printf("Hello, world.\n")
}
```
现在你可以用 go 工具构建并安装此程序了：
```javascript
$ go install github.com/user/hello
```
注意，你可以在系统的任何地方运行此命令。go 工具会根据 GOPATH 指定的工作空间，在 github.com/user/hello 包内查找源码。

若在从包目录中运行 go install，也可以省略包路径：
```javascript
$ cd $GOPATH/src/github.com/user/hello
$ go install
```
然后在命令行中输入它的完整路径来运行：
```javascript
$ $GOPATH/bin/hello
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190915153912453.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MDM3NzY5MQ==,size_16,color_FFFFFF,t_70)
最后将代码推送到GitHub远程仓库上，远程仓库的连接和配置过程请移步这篇博客
 [GitHub远程连接配置](https://blog.csdn.net/weixin_40377691/article/details/100853313)
![在这里插入图片描述](https://img-blog.csdnimg.cn/201909151448357.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MDM3NzY5MQ==,size_16,color_FFFFFF,t_70)
这里不得不提的是我在配置好GitHub的远程连接以后，不知道是因为改了哪里的配置，然后就导致go的所有指令都无法运行，报错信息是无法找到该指令。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190915161311311.jpeg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MDM3NzY5MQ==,size_16,color_FFFFFF,t_70)
导致出现这个问题的原因我现在还不清楚，猜测可能是不小心改了prifile的目录或者内容？但是我改回去也没有解决这个问题。

最后的解决办法是，重装大法好。重新`sudo yum install golang `，又检查了一遍配置，然后就可以了。

- **一个关于检查golang是否装好了的小tips：**
使用go env命令，如果出现如下图所示，则说明go配置正确
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190915161556327.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MDM3NzY5MQ==,size_16,color_FFFFFF,t_70)
##  2、你的第一个库
让我们编写一个库，并让 hello 程序来使用它。

同样，第一步还是选择包路径（我们将使用 github.com/user/stringutil） 并创建包目录：
```javascript
$ mkdir $GOPATH/src/github.com/user/stringutil
```
接着，在该目录中创建名为 reverse.go 的文件，内容如下：
```javascript
// stringutil 包含有用于处理字符串的工具函数。
package stringutil

// Reverse 将其实参字符串以符文为单位左右反转。
func Reverse(s string) string {
	r := []rune(s)
	for i, j := 0, len(r)-1; i < len(r)/2; i, j = i+1, j-1 {
		r[i], r[j] = r[j], r[i]
	}
	return string(r)
}
```
现在用 go build 命令来测试该包的编译：
```javascript
$ go build github.com/user/stringutil
```
当然，若你在该包的源码目录中，只需执行：
```javascript
$ go build
```
即可。这不会产生输出文件。想要输出的话，必须使用 go install 命令，它会将包的对象放到工作空间的 pkg 目录中。

确认 stringutil 包构建完毕后，修改原来的 hello.go 文件（它位于 $GOPATH/src/github.com/user/hello）去使用它：
```javascript
package main

import (
	"fmt"

	"github.com/user/stringutil"
)

func main() {
	fmt.Printf(stringutil.Reverse("!oG ,olleH"))
}
```
无论是安装包还是二进制文件，go 工具都会安装它所依赖的任何东西。 因此当我们通过
```javascript
$ go install github.com/user/hello
```
来安装 hello 程序时，stringutil 包也会被自动安装。
运行此程序的新版本，你应该能看到一条新的，反向的信息：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190915155156517.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MDM3NzY5MQ==,size_16,color_FFFFFF,t_70)
```javascript
bin/
	hello                 # 可执行命令
pkg/
	linux_amd64/          # 这里会反映出你的操作系统和架构
		github.com/user/
			stringutil.a  # 包对象
src/
	github.com/user/
		hello/
			hello.go      # 命令源码
		stringutil/
			reverse.go       # 包源码
```
Go的可执行命令是静态链接的；在运行Go程序时，包对象无需存在。
##  3、测试
Go拥有一个轻量级的测试框架，它由 go test 命令和 testing 包构成。

你可以通过创建一个名字以 _test.go 结尾的，包含名为 TestXXX 且签名为 func (t *testing.T) 函数的文件来编写测试。 测试框架会运行每一个这样的函数；若该函数调用了像 t.Error 或 t.Fail 这样表示失败的函数，此测试即表示失败。

我们可通过创建文件 $GOPATH/src/github.com/user/stringutil/reverse_test.go 来为 stringutil 添加测试，其内容如下：
```javascript
package stringutil

import "testing"

func TestReverse(t *testing.T) {
	cases := []struct {
		in, want string
	}{
		{"Hello, world", "dlrow ,olleH"},
		{"Hello, 世界", "界世 ,olleH"},
		{"", ""},
	}
	for _, c := range cases {
		got := Reverse(c.in)
		if got != c.want {
			t.Errorf("Reverse(%q) == %q, want %q", c.in, got, c.want)
		}
	}
}
```
接着使用 go test 运行该测试：
```javascript
$ go test github.com/user/stringutil
```
同样，若你在包目录下运行 go 工具，也可以忽略包路径
```javascript
$ go test
```
运行结果如下：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190915155827495.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MDM3NzY5MQ==,size_16,color_FFFFFF,t_70)
##  4、远程包
像Git或Mercurial这样的版本控制系统，可根据导入路径的描述来获取包源代码。go 工具可通过此特性来从远程代码库自动获取包。例如，本文档中描述的例子也可存放到Google Code上的Mercurial仓库 code.google.com/p/go.example 中，若你在包的导入路径中包含了代码仓库的URL，go get 就会自动地获取、 构建并安装它：
```javascript
$ go get github.com/golang/example/hello
$ $GOPATH/bin/hello
```
运行结果如下：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190915160142209.png)
若指定的包不在工作空间中，go get 就会将会将它放到 GOPATH 指定的第一个工作空间内。（若该包已存在，go get 就会跳过远程获取， 其行为与 go install 相同）

在执行完上面的go get 命令后，工作空间的目录树看起来应该是这样的：

```javascript
bin/
	hello                 # 可执行命令
pkg/
	linux_amd64/
		code.google.com/p/go.example/
			stringutil.a     # 包对象
		github.com/user/
			stringutil.a     # 包对象
src/
	code.google.com/p/go.example/
		hello/
			hello.go      # 命令源码
		stringutil/
			reverse.go       # 包源码
			reverse_test.go  # 测试源码
	github.com/user/
		hello/
			hello.go      # 命令源码
		stringutil/
			reverse.go       # 包源码
			reverse_test.go  # 测试源码
```
- 添加子对象
    `public static GameObject CreatePrimitive(PrimitiveType type);`
    Creates a game object with a primitive mesh renderer and appropriate collider.


- 清除所有子对象
```javascript
foreach (Transform child in transform) {  
    Destroy(child.gameObject);  
} 
```
更多详细go语言编程内容请移步：
[参考文档](https://go-zh.org/doc/code.html)
