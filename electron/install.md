## 安装

要安装预编译好的的二进制文件, 请使用 npm。 首选的方法是在项目中作为development dependency安装。

    npm install electron --save-dev

查看versioning doc获取如何在你的应用中管理Electron的相关信息。
### 全局安装

您还可以在 $PATH 中全局安装 electron 命令(如果需要在全局命令行中使用electron命令):

    npm install electron -g

### 自定义

如果想修改下载安装的位版本(例如, 在x64机器上安装ia32位版本), 你可以使用npm install中的--arch标记，或者设置npm_config_arch 环境变量:

    npm install --arch=ia32 electron

此外, 您还可以使用 --platform 来指定开发平台 (例如, win32、linux 等):

    npm install --platform=win32 electron

### 代理

如果需要使用 HTTP 代理, 则可以 设置这些环境变量 。
#### 自定义镜像和缓存

在安装过程中，electron 模块会通过 electron-download 为您的平台下载 Electron 的预制二进制文件。 这将通过访问 GitHub 的发布下载页面来完成 (https://github.com/electron/electron/releases/tag/v$VERSION, 这里的 $VERSION 是 Electron 的确切版本).

如果您无法访问GitHub，或者您需要提供自定义构建，则可以通过提供镜像或现有的缓存目录来实现。
#### 镜像

您可以使用环境变量来覆盖基本 URL，查找 Electron 二进制文件的路径以及二进制文件名。 使用 electron-download 的网址 组成如下：:

    url = ELECTRON_MIRROR + ELECTRON_CUSTOM_DIR + '/' + ELECTRON_CUSTOM_FILENAME

例如，使用中国镜像：

    ELECTRON_MIRROR="https://npm.taobao.org/mirrors/electron/"

#### 缓存

或者，您可以覆盖本地缓存。 electron-download 会将下载的二进制文件缓存在本地目录中，不会增加网络负担。 您可以使用该缓存文件夹来提供 Electron 的定制版本，或者避免进行网络连接。

    Linux: $XDG_CACHE_HOME or ~/.cache/electron/
    MacOS: ~/Library/Caches/electron/
    Windows: $LOCALAPPDATA/electron/Cache or ~/AppData/Local/electron/Cache/

在使用旧版本 Electron 的环境中，您也可以在~/.electron中找到缓存。

您也可以通过提供一个 ELECTRON_CACHE 环境变量来覆盖本地缓存位置。

缓存包含版本的官方zip文件以及校验和，存储为文本文件。 典型的缓存可能如下所示：

├── electron-v1.7.9-darwin-x64.zip
├── electron-v1.8.1-darwin-x64.zip
├── electron-v1.8.2-beta.1-darwin-x64.zip
├── electron-v1.8.2-beta.2-darwin-x64.zip
├── electron-v1.8.2-beta.3-darwin-x64.zip
├── SHASUMS256.txt-1.7.9
├── SHASUMS256.txt-1.8.1
├── SHASUMS256.txt-1.8.2-beta.1
├── SHASUMS256.txt-1.8.2-beta.2
├── SHASUMS256.txt-1.8.2-beta.3

#### 跳过二进制包下载

当您在安装 electron NPM 包时, 它会自动下载 electron 的二进制包。

当在CI环境中 测试另一个组件的时候，这可能是不必要的。

为了防止当您安装所有 npm 依赖关系时下载二进制文件，您可以设置环境变量 ELECTRON_SKIP_BINARY_DOWNODD。 例如:

    ELECRON_SKIP_BINARY_DOWNOAD=1 npm install

### 故障排查

在运行 npm install electron 时，有些用户会偶尔遇到安装问题。

在大多数情况下，这些错误都是由网络问题导致，而不是因为 electron npm 包的问题。 如 ELIFECYCLE、EAI_AGAIN、ECONNRESET 和 ETIMEDOUT 等错误都是此类网络问题的标志。 最佳的解决方法是尝试切换网络，或是稍后再尝试安装。

如果通过 npm 安装失败，您可以尝试直接从 electron/electron/releases 直接下载 Electron。

如果安装失败并出现 EACCESS 错误, 则可能需要 修复您的 npm 权限 。（例如使用 sudo ）

_这里出现EACCESS错误，使用 sudo 仍然无法解决，npm给出解决方法是用 nvm 重装 node 和 npm,按照方法重装后仍然不行；用第二种方法手动修改 npm 的 prefix 配置_

    mkdir ~/.npm-global
    npm config set prefix '~/.npm-global'
    export PATH=~/.npm-global/bin:$PATH
    source ~/.profile
    npm install -g electron@6.0.2

_这种方法适合没有使用 nvm 这种管理工具按照的 node 和 npm,我是用 nvm 安装的，在执行 source ~/.profile的时候会提示让你脱离 nvm 管理，所以没有采用这种方法；最后是加上了 --unsafe-prem=true,问题解决了。_


如果上述错误仍然存在, 则可能需要将参数 unsafe-perm 设置为 true

    npm install electron --unsafe-perm=true

_这里出现了坑，本来想通过镜像代理来安装，设置了 $ELECTRON_MIRROR 环境变量，可是如果采用 sudo 执行安装命令，再带上--unsafe-perm=true,那么npm是以超级用户的身份去执行，但环境变量是设置再user用户的，所以安装过程中不会去走镜像，而是走的github上的release,这很慢，甚至没有下载量，所以尝试去掉sudo 用user运行则可以安装成功_


在较慢的网络上, 最好使用 --verbose 标志来显示下载进度:

npm install --verbose electron

如果需要强制重新下载文件, 并且 SHASUM 文件将 force_no_cache 环境变量设置为 true。

    npm i --unsafe-prem=true --verbose electron@6.0.2 -g