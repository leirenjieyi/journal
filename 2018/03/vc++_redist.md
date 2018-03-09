# 确定要重新分发的 DLL
在生成由 Visual Studio 提供的 DLL 生成应用程序时，应用程序的用户也必须在其运行该应用程序的计算机上安装这些 DLL。 由于大多数用户可能没有安装 Visual Studio，因此你必须为他们提供这些 DLL。 Visual Studio 以可再发行库的形式提供这些 DLL，因此你将其包含到应用程序的安装程序中。

这些可再发行 DLL 随 Visual Studio 安装。 默认情况下，他们都安装在 Program Files (x86)\Microsoft Visual Studio version\VC\Redist 文件夹中。 若要更轻松地将它们包含到你的安装程序，也可从 Microsoft 下载中心以独立的可再发行组件包形式获得它们。 这些是安装用户的计算机上的可再发行文件的可执行文件。 可再发行组件包的版本必须与创建应用程序的 Visual Studio 工具集的版本相匹配。 若要查找匹配的可再发行组件包，请在 Microsoft 下载中心搜索“Visual C++ 可再发行组件包”。

若要确定必须与应用程序一起重新发布的 DLL，请收集应用程序所依赖的 DLL 列表。 收集该列表的一种方法是运行了解 Visual C++ 应用程序的依赖项中介绍的依赖项查看器 (depends.exe)。

具有依赖项列表时，将该列表与 Microsoft Visual Studio 安装目录中的所有 Redist.txt 文件中的列表进行比较，或者与你 Visual Studio 版本的 Microsoft 软件许可条款中“可分发代码”部分引用的可再发行 DLL“REDIST 列表”进行比较。 对于 Visual Studio 2013，列表可以在Microsoft Visual Studio 2013 和 Microsoft Visual Studio 2013 SDK 的可分发代码中联机获得。 无法重新发布 Visual Studio 中包含的所有文件；只允许重新发布 Redist.txt 或在线“REDIST 列表”中指定的文件。<font color=red>调试版本的应用程序和各种 Visual C++ 调试 DLL 是不可再发行的。</font>

# 重新发布 Visual C++ 库 三种方法

由于 Visual C++ 库由 Visual Studio 安装程序安装在 %windir%\system32\ 目录中，所以当你开发依赖这些库的 Visual C++ 应用程序时，它会按预期运行。 但是，若要将应用程序部署到可能没有 Visual Studio 的计算机，我们建议你确保库与应用程序一起安装在这些计算机中。在你的部署中，可以重新发布获得重新发布许可的任何版本的 Visual C++ 库。 以下是三种部署方法：

* 使用可再发行组件包进行集中部署，这些包将 Visual C++ 库作为共享 DLL 安装在 %windir%\system32\ 中。（安装到该文件夹中需要管理员权限。）你可以创建一个脚本或安装程序，以便在目标计算机上安装应用程序之前运行可再发行组件包。 可再发行组件包可用于 x86、x64 和 ARM 平台（VCRedist_x86.exe、VCRedist_x64.exe 或 VCRedist_arm.exe）。 Visual Studio 将这些包放置在 %ProgramFiles(x86)%\Microsoft Visual Studio version\VC\Redist\locale ID\ 中。 你还可以从 Microsoft Download Center（Microsoft 下载中心）下载这些包。（在 Download Center（下载中心）上搜索与你的应用程序匹配的“Visual C++ Redistributable Package Visual Studio version and update”。 例如，你使用了 Visual Studio 2012 Update 4 生成应用程序，则搜索“Visual C++ Redistributable Package 2012 Update 4”。）
* 使用合并模块进行集中部署，其中每个模块会将特定的 Visual C++ 库作为共享 DLL 安装在 %windir%\system32\ 中。（安装到该文件夹中需要管理员权限。）合并模块将成为你应用程序的 .msi 安装程序文件的一部分。 Visual C++ 可再发行合并模块包含在 Visual Studio 的 \Program Files (x86)\Common Files\Merge Modules\ 中。
* 本地部署，你要复制 Visual Studio 安装中特定的 Visual C++ DLL（一般位于 \Program Files (x86)\Microsoft Visual Studio version\VC\Redist\platform\library\ 中），然后将其安装在目标计算机上应用程序可执行文件所在的文件夹中。 你可以使用该部署方法启用无管理员权限的用户的安装，或可从网络共享运行的应用程序的安装。

如果部署使用可再发行合并模块，并且由无管理员权限的用户运行安装，则 Visual C++ DLL 不会安装，应用程序不会运行。 另外，通过允许按用户安装的合并模块生成的应用程序安装程序会将库安装在影响所有系统用户的共享位置中。 你可以使用本地部署在特定用户的应用程序目录中安装所需的 Visual C++ DLL，而不影响其他用户，也不需要管理员权限。 由于这可能造成可服务性问题，因此不建议本地部署 Visual C++ 可再发行 DLL。

Visual C++ 库的错误部署可能导致执行依赖于这些库的应用程序时出现运行时错误。 操作系统加载应用程序时，会使用 LoadLibraryEx 中介绍的搜索顺序。

除非你不必为应用程序提供服务并且不依赖于一个以上的 DLL 版本，否则我们建议你不要使用合并模块。 同一 DLL 的不同版本的合并模块不能包含在一个安装程序中，而且合并模块使你难以独立于应用程序为 DLL 服务。 