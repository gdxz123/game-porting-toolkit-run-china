# game-porting-toolkit-run-china
在中国，MacOS使用game porting toolkit 移植windows游戏教程

文章从https://www.applegamingwiki.com/wiki/Game_Porting_Toolkit 机器翻译加我人工校正修改而来，同时我增加了适合中国网络环境的方案（因为网络的原因），这个文章是目前最全且稳定的运行苹果Game Porting Toolkit的方案，github为最新版本：https://github.com/gdxz123/game-porting-toolkit-run-china/

苹果游戏移植工具(Game Porting Toolkit)
文章运行的电脑：MacBook pro M1
注意: Game Porting Toolkit 文内简称GPTK

游戏移植工具包是苹果于2023年6月6日发布的新的windows程序转译层。游戏移植工具包（GPTK）将Wine与苹果自己的D3DMetal相结合，后者支持DirectX 11和12。与CrossOver或Parallels相比，这是一种在Apple Silicon Mac上安装Windows版游戏的用户体验更好的方案，它解锁了玩许多DirectX 12游戏的能力。很多游戏使用GPTK能正常运行，然而，使用了反作弊技术或攻击性数字版权校验以及需要AVX/AVX 2的游戏可能没法顺利正常运行，，例如《The Last of Us》第一章。

**（如果你想要安装的是战网(Battle.net)软件，那你的系统语言一定要设置成英文，否则会遇到一直奔溃的问题，在系统设置中语言和地区处可以修改系统语言，修改后需要重启电脑）** 

## GPTK安装流程介绍
### 一、安装前要求
- 1、应该使用macOS Sonoma，目前它处于测试版。您可以从[MrMacintosh博客](https://juejin.cn/post/7088877842842255368)下载pkg安装程序。
- 2、macOS Ventura会导致大量steamwebhelper.exe崩溃问题，因此建议使用macOS Sonoma测试版。
- 3、访问苹果开发者下载网站，这些文件现在可以免费下载，供任何登录的苹果帐户使用。
- 4、如果您安装了旧版本的Xcode，请将其删除。
- 5、搜索Command Line Tools for Xcode15beta，下载dmg文件，然后安装它。
- 6、搜索Game Porting Toolkit并下载它。打开dmg文件，然后运行pkg。

### 二、X86_64版本的Homebrew安装
注意：如果你以前安装过Homebrew，那么建议删除ARM64版本的Homebrew因为这会干扰这个安装过程。使用删除文件夹/opt/homebrew/bin或使用Homebrew卸载脚本。如果没有/opt/homebrew这个目录说明没有安装arm版Homebrew，那就不用管。如果您喜欢同时安装ARM64和x86版本的brew，您可以在.zshrc文件中添加一个“brew切换器”，以允许根据活动架构使用任何一个版本。安装Brew后，您可以按照本节末尾的步骤来实现这一点。

打开终端Terminal（在macOS上的Spotlight 中搜索“Terminal”，Spotlight快捷键是Command+空格）

在Terminal写入命令行安装Rosetta：
```
softwareupdate -—install-rosetta
```

切换到x86_64版本的shell因为Rosetta需要x86环境执行。所有后续命令都应该在这个x86版本的shell中运行（如果意外关闭了Terminal，重启后要再切换到x86版本）。

在Terminal写入命令行切换shell版本：
```
arch -x86_64 zsh
```

如果您还没有安装x86_64版本的Homebrew，请安装它：
```
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

注意这个命令在中国大概率成功不了，因为链接被墙了。我们用国内的镜像链接来安装：
```
/bin/bash -c "$(curl -fsSL https://gitee.com/ineo6/homebrew-install/raw/master/install.sh)"
```

配置环境路径：
```
(echo; echo 'eval "$(/usr/local/bin/brew shellenv)"') >> /Users/$USER/.zprofile
eval "$(/usr/local/bin/brew shellenv)"
```

用下面指令确认安装的brew在你电脑上：
```
which brew
```

如果此命令不打印/usr/local/bin/brew，则应使用此命令：
```
export PATH=/usr/local/bin:${PATH}
```

### 三、构建GPTK
运行此命令下载Apple tap：
```
brew tap apple/apple http://github.com/apple/homebrew-apple
```

注意这个也是大概率下载不成功，原因你懂的
我这边找到国内镜像包来安装：
```
brew tap apple/apple https://gitclone.com/github.com/apple/homebrew-apple.git
```

安装游戏移植工具包公式。这个公式下载并编译了几个大型软件项目。这需要多长时间取决于计算机的速度。根据Mac的速度，可能需要1个多小时才能完成：
```
brew -v install apple/apple/game-porting-toolkit
```

下载速度很慢，有科学上网工具的可以给Terminal配置代理加快速度，没有就等它下载构建完。

(非必需步骤)如果在安装过程中您看到诸如“错误：游戏移植工具包：未知或不支持的macOS版本：：dunno”之类的错误，则您的Homebrew版本不支持macOS Sonoma。请更新至Homebrew的最新版本，然后用下面的命令重试：
```
brew update brew -v install apple/apple/game-porting-toolkit
```

（非必需步骤)有时会遇到错误，Error: Tap apple/apple remote mismatch
这可能时前面运行
```
brew tap apple/apple http://github.com/apple/homebrew-apple
```
命令时拉取了一些配置，导致前后对不上
运行下面指令：
```
brew untap apple/apple
brew tap apple/apple https://gitclone.com/github.com/apple/homebrew-apple.git
```
再去运行
```
brew update brew -v install apple/apple/game-porting-toolkit
```

### 四、配置GPTK
确保之前下载的游戏移植工具包dmg安装在/Volumes/Game Porting Toolkit-1.0上。使用此下面命令将Game Porting Toolkit库目录复制到Wine的库目录中：
```
ditto /Volumes/Game\ Porting\ Toolkit-1.0/lib/ `brew --prefix game-porting-toolkit`/lib/
```

使用以下命令将游戏移植工具包DMG中的3个脚本放入/usr/local/bin中：
```
cp /Volumes/Game\ Porting\ Toolkit*/gameportingtoolkit* /usr/local/bin
```

### 五、Wine前缀配置
Wine前缀包含一个虚拟C盘驱动器，类似于CrossOver中的Bottle。您将把工具包和游戏安装到这个虚拟C盘驱动器中。运行以下命令在您的主目录中创建一个新的Wine前缀，名为my game prefix。这将创建一个名为“my-game-prefix”的Wine前缀，但可以重命名为任何前缀：
```
WINEPREFIX=~/my-game-prefix `brew --prefix game-porting-toolkit`/bin/wine64 winecfg
```

执行后应该：
- 1、屏幕上应出现“Wine configuration”窗口。
- 2、我们将Windows的版本更改为Windows 10。
- 3、选择“应用”，然后选择“确定”退出Wine configuration界面。

如果“Wine configuration”窗口未出现，并且Dock中未显示新图标，请验证您是否已正确安装了x86_64版本的Homebrew以及游戏移植工具包公式。

现在，您已经准备好将启动器或单个Windows游戏安装到此Wine前缀中，请参见下文。

### 六、安装各种主流程序

目前发现浏览器开启可能会影响Game Porting Toolkit运行，最好把浏览器都完全关闭，然后再去运行下面程序

#### Steam：
- 1、下载Windows版本的[Steam](https://cdn.cloudflare.steamstatic.com/client/installer/SteamSetup.exe)并将其放在下载文件夹中。
- 2、用命令行安装Windows版本Steam:
```
gameportingtoolkit ~/my-game-prefix ~/Downloads/SteamSetup.exe
```
- 3、用命令行运行Steam
```
gameportingtoolkit ~/my-game-prefix 'C:\Program Files (x86)/Steam/steam.exe'
```
- 4、登录Steam

一个常见的问题是Steam将显示一个空白的黑色窗口。

解决方案1(可试)：Steam的替代运行方式（安装后）：
```
MTL_HUD_ENABLED=1 WINEESYNC=1 WINEPREFIX=~/my-game-prefix /usr/local/Cellar/game-porting-toolkit/1.0/bin/wine64 'C:\Program Files (x86)\Steam\steam.exe'
```

解决方案2（不推荐）:如果继续出现这种情况，请关闭“终端”窗口，然后重新打开并重试，重复此操作，直到登录屏幕打开。现在你应该可以通过Steam下载并启动Windows游戏了。

解决方案3(推荐）:使用其他端登录好的状态拷贝到Wine里面的Steam去。登录Mac版本Steam，然后复制三个文件/文件夹：config、registry.vdf、userdata到~/my-game-prefix/drive_c/Program Files (x86)/Steam/


- 5、如果运行Steam，只看到APP图标，没看到画面，把除了终端之外所有的窗口（包括浏览器）全关掉，用killall -9 wineserver && killall -9 wine64-preloader 命令关掉之前创建的 wine 进程，再重新启动Steam

#### Battle.net(战网软件)：
下载[Battle.net的Windows版本](https://download.battle.net/en-gb/?platform=windows)，并将其放入“下载”文件夹中。

**（ 如果系统语言不是英文，需要把系统语言切换到英文，然后会重启电脑，因为系统非英文兼容有问题。重启电脑后，把~/my-game-prefix文件夹删除，再继续下面的流程。可以用**
```
open ~ 
```
**命令快速找到my-game-prefix文件夹）** 

为战网制作一个新的Wineprefix前缀，你可以选择使用my-game-prefix 前缀或将其更改为其他任何前缀:
```
WINEPREFIX=~/my-game-prefix `brew --prefix game-porting-toolkit`/bin/wine64 winecfg
```

执行后应该：
- 1、屏幕上应出现“Wine configuration”窗口。
- 2、我们将Windows的版本更改为Windows 10。
- 3、选择“应用”，然后选择“确定”退出Wine configuration界面。

如果你正在运行暗黑破坏神IV，那么你需要运行此脚本来更新Wineprefix，使其显示为最新版本的Windows：
```
WINEPREFIX=~/my-game-prefix `brew --prefix game-porting-toolkit`/bin/wine64 reg add 'HKEY_LOCAL_MACHINE\Software\Microsoft\Windows NT\CurrentVersion' /v CurrentBuild /t REG_SZ /d 19042 /f
```
```
WINEPREFIX=~/my-game-prefix `brew --prefix game-porting-toolkit`/bin/wine64 reg add 'HKEY_LOCAL_MACHINE\Software\Microsoft\Windows NT\CurrentVersion' /v CurrentBuildNumber /t REG_SZ /d 19042 /f
```
```
WINEPREFIX=~/my-game-prefix `brew --prefix game-porting-toolkit`/bin/wineserver -k
```

安装Battle.net
```
gameportingtoolkit ~/my-game-prefix ~/Downloads/Battle.net-Setup.exe
```

命令行启动战网
```
gameportingtoolkit ~/my-game-prefix 'C:\Program Files (x86)/Battle.net/Battle.net.exe'
```

请注意，一旦安装，启动Battle.net就会出现问题，当前唯一的重新登录方法是再次“安装”战网。
使用以下命令在没有启动战网的情况下开始暗黑4游戏：
```
arch -x86_64 gameportingtoolkit-no-hud ~/my-game-prefix 'C:\Program Files (x86)\Diablo IV\Diablo IV Launcher.exe'
```

### 七、通用
安装单独的exe游戏：在Finder中打开Wine前缀的虚拟C盘驱动器（打开~/my-game-prefix/drive_C），并将游戏复制到适当的子目录中。

A.标准加载运行
```
gameportingtoolkit ~/my-game-prefix 'C:\Program Files (x86)\Steam\xxxx.exe'
```

这启动了给定的Windows游戏二进制文件，带有可见的扩展Metal性能HUD，并过滤从游戏移植工具包输出的日志记录。

B.加载运行没有性能显示框
```
gameportingtoolkit-no-hud ~/my-game-prefix 'C:\Program Files (x86)\Steam\xxxx.exe'
```

C.加载运行关闭ESYNC的Wine
```
gameportingtoolkit-no-esync ~/my-game-prefix 'C:\Program Files (x86)\Steam\xxxx.exe'
```

打开Wine配置界面:
```
gameportingtoolkit ~/my-game-prefix winecfg
```

Steam在打开后立即崩溃：
断开任何多个屏幕，只用一个屏幕

战网启动程序不会重新启动：
重新安装启动器以重新打开，目前没有其他修复程序。

Windows版本太久：
我的游戏不会运行，因为它认为Windows版本太旧了。一些游戏检测到特定的最低版本的Windows，需要更新。使用此脚本更新您的wineprefix版本19042，该版本应适用于大多数游戏，例如《蜘蛛侠重制版》。
输入命令行：
```
WINEPREFIX=~/my-game-prefix `brew --prefix game-porting-toolkit`/bin/wine64 reg add 'HKEY_LOCAL_MACHINE\Software\Microsoft\Windows NT\CurrentVersion' /v CurrentBuild /t REG_SZ /d 19042 /f
```
```
WINEPREFIX=~/my-game-prefix `brew --prefix game-porting-toolkit`/bin/wine64 reg add 'HKEY_LOCAL_MACHINE\Software\Microsoft\Windows NT\CurrentVersion' /v CurrentBuildNumber /t REG_SZ /d 19042 /f
```
```
WINEPREFIX=~/my-game-prefix `brew --prefix game-porting-toolkit`/bin/wineserver -k
```

steamwebhelper.exe奔溃：
这是由于Steam是通过macOS Ventura或更低版本运行的，大多数用户应该升级到macOS Sonoma。您可以使用CrossOver解决方法登录到Steam，但会不断发生崩溃。

如果奔溃后没有命令不响应：
尝试关闭所有Wine线程
```
killall -9 wineserver && killall -9 wine64-preloader
```

反作弊系统和数字版权系统：
可能不会有任何使用反作弊的游戏的修复，例如轻松反作弊。有些游戏有变通办法，例如Elden Ring。其他使用Denuvo的游戏也可能是不兼容的，直到该DRM被游戏开发商删除。



<image src="./image/battle.jpg" width="600"/>



### 八、游戏移植测试情况
游戏移植Mac测试情况(Playable-可玩，Runs-能运行，Perfect-完美)：

暗黑4                                        Perfect 

赛博朋克2077                          Perfect

艾尔登法环                               Perfect

帝国时代 II                                Playable

最终幻想 VII Remake Intergrade        Perfect

最终幻想 XIV                            Perfect

光晕 3                                        Playable

虚拟人生 4                                Perfect

巫师 3                                        Playable

传送门                                        Playable

传送门 2                                     Playable

GTA V                                        Perfect

Astroneer                                   Perfect

QUBE                                          Perfect

Batman Arkham Knight            Perfect

BioShock Infinite                       Runs

Codename: Loop                       Perfect

Cogmind                                     Perfect

Control                                        Perfect

Disco Elysium                            Perfect

EVE Online                                Perfect

Fallout: New Vegas                    Playable

Freeways                                    Perfect

Genshin Impact                            Perfect

Hatsune Miku: Project DIVA Mega Mix+        Perfect

Hogwarts Legacy                        Playable

Jazz Jackrabbit 2                    Perfect

L.A. NoireRunsMagicite            Perfect

One piece odessey                    Perfect

Osu!PerfectOuter Wilds                Playable

Risk of Rain 2                                Perfect

Rocket League                            Playable

Satisfactory                                Playable

Spore                                        Perfect

Valheim                                        Playable

XIII                                                Perfect

.........(还有很多，具体看移植测试)
