## npm 本地包与全局包
1. 如果你自己的模块依赖于某个包，并通过 Node.js 的 require 加载，那么你应该选择本地安装，这种方式也是 npm install 命令的默认行为。
2. 如果你想将包作为一个命令行工具，（比如 grunt CLI），那么你应该选择全局安装。

*本地安装*

    npm install json 

本地安装json包,上述命令执行之后将会在当前的目录下创建一个 node_modules 的目录（如果不存在的话），然后将下载的包保存到这个目录下。

*全局安装*

    npm install -g jshint

## 包管理
最好的管理方式是在本地创建一个 package.json 文件，在这个文件中：

1. 列出了项目依赖的包
2. 允许使用语义化版本规则指定依赖包的版本
3. 能更好的打包分享

__创建一个 package.json 文件__
    
    npm init
or
    
    npm init --yes
