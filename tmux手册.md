# 摘要
tmux	[-2Cluv] [-c shell-command] [-f file] [-L socket-name] [-S socket-path] [command [flags]]
# 描述
 * tmux可以在一个屏幕中创建多个session，window，pane等，可以从一个屏幕中分离并继续在后台运行，以后可以重新连接。
 * 当tmux启动，将会城建一个新的session和一个独立的window。
 * 底部状态行显示当前session的信息并且可以用来输入交互命令。
 * 一个session是一个独立的tmux管理的伪终端集合。
 * 每个session可以有一个或者多个window连接。
 * 一个window占据整个屏幕并且可以分割成多个矩形pane，每个pane是一个独立的伪终端。
 * 任意数量的tmux实例可以连接都同一个session，任意数量的window可以显示同一个session。
 * 一旦所有sessions被杀死，tmux将会退出。
 * 如果意外断开（例如ssh连接超时），或者使用命令'C-b d'分离，session将会被保存。tmux可以使用下面的命令重新连接session：
 :`` $ tmux attach ``
 * 在tmux中，session通过client端显示在屏幕中，所有的session通过一个独立的server进行管理。
 * server和每个client都是独立的进程，它们使用/tmp中的socket进行通讯。
 * 下面是tmux的参数项：
  * -2 强制tmux使用256色
  * -C 使用控制模式启动（参考CONTROL MODE章节). 使用(-CC)禁用echo.
  * -c shell-command 执行shell命令使用默认的shell。如果需要，tmux服务会开始重新检索default-shell选项。这个选项是为了当tmux用来作为登录shell时兼容sh(1)。
  * -f file 指定配置文件。默认情况，tmux从/etc/tmux加载系统配置文件，然后寻找~/.tmux.conf用户配置文件。tmux在server进程启动时加载配置一次。source-file命令可以用来加载配置文件。tmux在第一个创建的session的命令行中显示配置文件的错误信息，并继续加载其他配置。
  * -L socket-name tmux server的套接字存储在目录TMUX_TMPDIR下面，如果未设置，则默认设置在/tmp目录下面。默认的套接字名称为default。这个参数允许指定套接字名称，允许运行多个独立的tmux server。除非指定-S参数，否则不需要完整的路径：创建的套接字都在相同的目录中。如果套接字意外丢失，SIGUSR1信号将会发送给tmux server进程并重新创建套接字（注意：如果父目录丢失，该操作将会失败）。
  * -l Behave as a login shell. This flag currently has no effect and is for compatibility with other shells when using tmux as a login shell.
  * -S socket-path 指定server套接字的完整路径。如果指定-S参数，将不会使用默认的套接字目录，并忽略-L参数。
  * -u tmux通过检测LC_ALL,LC_TYPE和LANG环境变量的第一个设置是否为"UTF-8"来尝试猜测终端是否有可能支持UTF-8。这并部总是正确的：-u标志明确告知tmux，支持utf-8。如果server从一个指定-u参数的client启动，utf8和status-utf8选项在session和window中被全局启用。
  * -v 要求详细的日志记录。这个参数可以通过指定多次来增加详细度。日志消息保存在当前目录的tmux-client-PID.log和tmux-server-PID.log文件中。
  * command [flags] 制定一组命令用来控制tmux，如下面章节所属。如果未指定任何命令，默认使用new-session命令。

# 按键绑定

默认的按键绑定:

|分类|按键|描述|替换方案|
| ------------- |:-------------|:-------------|:-----|
|基本操作|C-b	|命令按键前缀 				|使用C-a（和emacs按键冲突）或者C-k进行替换|
|基本操作|:  	|进入tmux命令行提示			|	|
|基本操作|?  	|显示所有的键盘绑定			|	|
|基本操作|t  	|显示时间				|	| 
|基本操作|~  	|从tmux中显示以前的消息，如果有的话	|	|
|基本操作|C-z	|挂起tmux客户端 			|	|
|基本操作|D  	|交互模式分离客户端			|	| 
|基本操作|d  	|分离当前客客户端			|	| 
|基本操作|r  	|强制重绘连接的客户端			|通常将这个键绑定为重新加载配置文件| 
|会话	 |$  	|重命名当前会话				|	|
|会话	 |(  	|将客户端切换到以前的会话		|	|
|会话	 |)  	|将客户端切换到下一个会话		|	|
|会话	 |L  	|将客户端切换回上一个会话		|	| 
|会话	 |s  	|交互模式切换客户端会话			|	| 
|窗口	 |0 to 9|根据选择的索引切换窗口			|	|
|窗口	 窗口	 |f  	|在打开的窗口交互模式搜索窗口		|命令行提示输入搜索文本，可以是索引也可以是窗口名称	|  
|窗口	 |w  	|交互模式选择当前窗口			|显示窗口列表进行选择	| 
|窗口	 |'  	|交互模式切换窗口			|命令行提示输入索引	|
|窗口	 |,  	|重命名当前窗口				|	|
|窗口	 |&  	|杀死当前窗口				|	|
|窗口	 |i  	|显示当前窗口的一些信息			|	|  
|窗口	 |n  	|切换到后一个窗口			|	|  
|窗口	 |p  	|切换到前一个窗口			|	| 
|窗口	 |c  	|创建新窗口				|	| 
|窗口	 |.	|重设当前窗口的索引	|不能选择已经存在的索引，只能选择还没被占用的，可以从0开始，意味着将当前窗口排在第一个	|
|窗口	 |l  	|切换到上一次的窗口			|	| 
|窗口	 |C-o	|将当前窗口向前移动 			|效果不明|
|窗口	 |M-n 	|移动到下一个窗口，响铃或激活标记	|	|
|窗口	 |M-p 	|移动到以前的窗口，响铃或激活标记	|	|
|面板	 |z  	|把当前pane最大化/恢复			|	|  
|面板	 |"  	|将当前面板纵向分割为上下2个 		|使用_替换|
|面板	 |%  	|将当前面板横向分割为左右2个 		|使用\|替换|
|面板	 |!  	|将当前面板拆离为一个窗口 		|	|
|面板	 |;  	|移动到以前激活的面板			|	|
|面板	 |o  	|切换到下一个面板			|	|  
|面板	 |q  	|显示面板索引				|	| 
|面板	 |x  	|杀死当前面板				|	|  
|面板	 |{  	|把当前的面板移到左边			|	|  
|面板	 |}  	|把当前的面板移到右边			|	|
|面板	 |m  	|标记当前pane(参见select-pane -m)	|	| 
|面板	 |M  	|清除标记的pane				|	| 
|面板	 |Space |切换到下一个系统默认布局		|	| 
|面板	 |Up, Down Left, Right|切换到当前面板的上下左右面板|使用自定义的类vi快捷键代替|
|面板	 |M-1 to M-5|Arrange panes in one of the five preset layouts: even-horizontal, even-vertical, main-horizontal, main-vertical,or tiled.Space Arrange the current window in the next preset layout.|	|
|面板	 |M-o |Rotate the panes in the current window backwards.	|	|
|面板	 |C-Up, C-Down C-Left, C-Right|调整面板的大小，每次1个单位	|	|
|面板	 |M-Up, M-Down M-Left, M-Right|调整面板的大小，每次5个单位	|	|
|文本操作|#  	|列出所有粘贴buffers			|	|
|文本操作|-  	|删除最近靠拷贝的文本buffer		|	| 
|文本操作|=  	|交互式从列表中选择粘贴哪一个buffer	|	|
|文本操作|[  	|进入复制模式或者显示历史		|	| 
|文本操作|]  	|进入粘贴模式				|	| 
|文本操作|Page Up|进入拷贝模式并向上滚动一页		|	|

键盘绑定可以通过bind-key和undind-key命令进行改变。

# 命令
本章包含tmux支持的命令列表。
大多数命令接受可选的参数-t(有时是-s) <target-client/target-session/target-window/target-pane>。这些参数指定了命令应该影响哪个cleint,session,window或者pane。
* target-client应该是客户端连接的pty(4)文件名称，例如：/dev/ttyp1或者ttyp1。如果没有制定客户端，tmux尝试计算出当前使用的客户端；如果失败，将会报告一个错误。客户端可以使用命令list-clients列出。
* target-session按照下面的顺序进行尝试：
  1. 前缀是$的session ID。
  2. session的确切名称（使用list-session命令列出）
  3. session名称的开始字符串，例如“mesess”将匹配到名称为“mysession”的session。
  4. An fnmatch(3) pattern which is matched against the session name.
  5. 如果session名称的前缀是'='，则表示使用精确匹配模式（“=mysess”只能匹配出“mysess”，而不能匹配到“mysession”）
  6. 如果只发现一个session，则使用目标session；如果匹配到多个，则引发一个错误。如果session被遗漏，将使用当前有效session；如果没有当前有效session，则选择最近使用的session。
* target-window 使用session:window的形式制定window。window按照下面的顺序查找：
  1. 制定的token，在下面列出。
  2. 一个window索引，例如：“mysession:1”指代“mysession”中的window 1
  3. window ID，例如@1
  4. 精确匹配的window名称，例如“mysession:mywindow”。
  5. 使用起始字符串模糊匹配，例如：“mysession:mywin”
  6. As an fnmatch(3) pattern matched against the window name.
  7. 就像session一样，使用'='前缀进行精确匹配。空的window名称指定下一个合适的未使用索引（例如：new-window和link-window命令）否则使用当前window。
  
下面指定的tokens表示特殊的window。

|Token|Meaning|
| ------------- |:-----|
|{start} ^|序号最小的window|
|{end} $|序号最大的window|
|{last} !|上一个window（相对于当前window的前面）|
|{next +|指定数字的下一个window|
|{previous} -|指定数字的前一个window|

* target-pane 可能是一个pane ID或者和target-window类似的形式但是可以选择使用句号家伙是那个pane索引或者ID，例如“myession:mywindow.1”。如果省略了pane索引，则使用指定window当前激活的pane。下面是一些可以用作pane索引的特殊token：

|Token		|Meaning|
| ------------- |:-----|
|{last}	!	|The last (previously active) pane|
|{next}	+	|The next pane by number|
|{previous}-	|The previous pane by number|
|{top}		|The top pane|
|{bottom}	|The bottom pane|
|{left}		|The leftmost pane|
|{right}	|The rightmost pane|
|{top-left}	|The top-left pane|
|{top-right}	|The top-right pane|
|{bottom-left}	|The bottom-left pane|
|{bottom-right}	|The bottom-right pane|
|{up-of}	|The pane above the active pane|
|{down-of}	|The pane below the active pane|
|{left-of}	|The pane to the left of the active pane|
|{right-of}	|he pane to the right of the active pane|

‘+’和‘-’ token可以使用下标，例如：select-window -t:+2  
  
详情参考[tmux man](http://www.openbsd.org/cgi-bin/man.cgi/OpenBSD-current/man1/tmux.1?query=tmux&sec=1 "tmux man")
