##Mac的Dashboard用来显示股票、日历、天气预报等小widget，应该在Leopard时代就有了。可以说是遗迹。不用的话可以关掉。

- 关闭Dashboard：
打开“终端”，输入命令：
defaults write com.apple.dashboard mcx-disabled -boolean YES
继续输入命令，重启Dock：
killall Dock

- 如果想再重新开启，则使用下面的命令：
defaults write com.apple.dashboard mcx-disabled -boolean NO
继续输入命令，重启Dock：
killall Dock