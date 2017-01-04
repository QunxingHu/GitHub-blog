---
title: Sublime text3 配置及常见问题排解
date: 2017-1-3 6:16:23 PM
categories:
- 术业专攻
tags: 
- sublime
---
各种IDE用了不少，不是卡就是慢，要不就是功能单一，自从使用了sublime, 各种神清气爽，当然也踩了不少坑，为了避免重复掉坑，记录下自己的一些填坑过程，同时也希望能给其他碰到相同问题的人一些借鉴，如有不当之处，欢迎批评指正。

<!-- more -->
## 安装激活
### 安装
sublime 支持 ubuntu 和 windows. 
1. [下载地址][1]，根据需要选择对应版本
2. ubuntu版本为 deb安装包，双击安装，windows下为exe文件，双击安装。
### 激活
**条件允许的话请使用正版，支持作者开发。**
**激活码**：
```
—– BEGIN LICENSE —–
Michael Barnes
Single User License
EA7E-821385
8A353C41 872A0D5C DF9B2950 AFF6F667
C458EA6D 8EA3C286 98D1D650 131A97AB
AA919AEC EF20E143 B361B1E7 4C8B7F04
B085E65E 2F5F5360 8489D422 FB8FC1AA
93F6323C FD7F7544 3F39C318 D95E6480
FCCC7561 8A4A1741 68FA4223 ADCEDE07
200C25BE DBBC4855 C4CFB774 C5EC138C
0FEC1CEF D9DCECEC D3A5DAD1 01316C36
—— END LICENSE ——

—– BEGIN LICENSE —–
Free Communities Consultoria em Informática Ltda
Single User License
EA7E-801302
C154C122 4EFA4415 F1AAEBCC 315F3A7D
2580735A 7955AA57 850ABD88 72A1DDD8
8D2CE060 CF980C29 890D74F2 53131895
281E324E 98EA1FEF 7FF69A12 17CA7784
490862AF 833E133D FD22141D D8C89B94
4C10A4D2 24693D70 AE37C18F 72EF0BE5
1ED60704 651BC71F 16CA1B77 496A0B19
463EDFF9 6BEB1861 CA5BAD96 89D0118E
—— END LICENSE ——

—– BEGIN LICENSE —–
Nicolas Hennion
Single User License
EA7E-866075
8A01AA83 1D668D24 4484AEBC 3B04512C
827B0DE5 69E9B07A A39ACCC0 F95F5410
729D5639 4C37CECB B2522FB3 8D37FDC1
72899363 BBA441AC A5F47F08 6CD3B3FE
CEFB3783 B2E1BA96 71AAF7B4 AFB61B1D
0CC513E7 52FF2333 9F726D2C CDE53B4A
810C0D4F E1F419A3 CDA0832B 8440565A
35BF00F6 4CA9F869 ED10E245 469C233E
—— END LICENSE ——
```
## 常用插件
- **package control**
	  `package control`是用于安装管理sublime的插件的组件
	  安装方法：[官方文档][2]
	  - 使用 ``` ctrl+`  ```　打开sublime终端
	  - 复制以下代码并粘贴到终端
	 ```
	  import urllib.request,os,hashlib; h = 'df21e130d211cfc94d9b0905775a7c0f' + '1e3d39e33b79698005270310898eea76'; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); urllib.request.install_opener( urllib.request.build_opener( urllib.request.ProxyHandler()) ); by = urllib.request.urlopen( 'http://packagecontrol.io/' + pf.replace(' ', '%20')).read(); dh = hashlib.sha256(by).hexdigest(); print('Error validating download (got %s instead of %s), please try manual install' % (dh, h)) if dh != h else open(os.path.join( ipp, pf), 'wb' ).write(by)
	 ```
- **Sublime CodeIntel**
  Sublime​Code​Intel 是一个代码提示、补全插件，支持 JavaScript、Mason、XBL、XUL、RHTML、SCSS、Python、HTML、Ruby、Python3、XML、Sass、XSLT、Django、HTML5、Perl、CSS、Twig、Less、Smarty、Node.js、Tcl、TemplateToolkit 和 PHP 等语言，是 Sublime Text 自带代码提示功能的很好扩展。它还有一个功能就是跳转到变量、函数定义的地方，十分方便。    
  使用 Sublime​Code​Intel 之前你需要安装相应程序并把路径写入 ~/.codeintel/config 或 project_root/.codeintel/config 中，ReadMe 中有详细的 说明，不再赘述。
  
- **Alignment**
  Alignment 是一个代码格式化插件，它可以使多行代码中的等号对齐，也可以调整多行代码为一个缩进级别，默认快捷键是 ctrl+alt+a（Mac OS 上是 cmd+ctrl+a）。
  
- **Trimmer**
  去掉多余空格
  
- **Bracket Highlighter**
  Bracket​Highlighter 是一个括号、引号、标签高亮插件，支持 []、()、{}、""、'' 和 <tag></tag> 等，比 Sublime Text 自带的高亮要明显得多。
  
- **GBK to UTF8**
	中文支持
 
- **Git**
  集合git 功能

- **SublimeLinter**
  代码分析检测工具
  
- **Emmet**
   按下Tab键，Emmet就能把一个缩写展开成一个HTML和CSS代码块，我想提一下Hayaku-集合了方便的层叠样式表缩写。前端必备。

- **SideBarEnhancements**
  ideBarEnhancements 是一款很实用的右键菜单增强插件，有以 diff 形式显示未保存的修改、在文件管理器中显示该文件、复制文件路径、在侧边栏中定位该文件等功能，也有基础的诸如新建文件/目录，编辑，打开/运行，显示，在选择中/上级目录/项目中查找，剪切，复制，粘贴，重命名，删除，刷新等常见功能。
SideBarEnhancements 还有一个功能就是自定义打开文件的程序，在侧边栏中右键点击一个文件（夹），选择 Open With -> Edit Applications 就可以修改关联了，配置文件自带示例，可以很方便地套用。

- **Markdown Extended**
  SublimeText 不仅仅是能够查看和编辑 Markdown 文件，但它会视它们为格式很糟糕的纯文本。这个插件通过适当的颜色高亮和其它功能来更好地完成这些任务。
  
- **SyncedSideBar**
   当编辑某个文件时，该插件能在左边栏高亮该文件
   
- **SyncedSideBarBg**
   同步 sidebar 与 编辑器的背景色，强迫症必备
   
- **File Icon**
  给不同类型的文件添加不同的图标
  
- **Docblockr**
  用来写注释的。在某个函数顶部敲 `/**` 再按回车
  
## **主题字体配置**
主题在 github 上可以找到很多，也可以自己定义。附加一个可以自行编辑主题的 [网站][4]

## **编译器配置**


## **常见问题**
### **ubuntu 上中文输入问题**
Sublime利用Fcitx输入中文（**注意: 注意这是针对Fcitx的方法, 针对IBus是不行的(经测试). Ibus可以去搜一个叫InputHelper来尝试..**）
- **简单的方法**
	最简单自动化方法是使用[lyfeyaj的git库][3]自动处理:
	```bash
	git clone https://github.com/lyfeyaj/sublime-text-imfix.git
	cd sublime-text-imfix && ./sublime-imfix
	```

- **自己动手**
	- **编写一个`sublime_imfix.c`文件(内容如下):**
	```c
		#include <gtk/gtkimcontext.h>
		void gtk_im_context_set_client_window (GtkIMContext *context,
				 GdkWindow  *window)
		{
		 GtkIMContextClass *klass;
		 g_return_if_fail (GTK_IS_IM_CONTEXT (context));
		 klass = GTK_IM_CONTEXT_GET_CLASS (context);

		 if (klass->set_client_window)
		   klass->set_client_window (context, window);

		 g_object_set_data(G_OBJECT(context),"window",window);

		 if(!GDK_IS_WINDOW (window))
		   return;

		 int width = gdk_window_get_width(window);
		 int height = gdk_window_get_height(window);

		 if(width != 0 && height !=0)
		   gtk_im_context_focus_in(context);
		}
   ```
   - **编译`libsublime-imfix.so`**
	```bash
	sudo apt-get install libgtk2.0-dev
	gcc -shared -o libsublime-imfix.so sublime_imfix.c  `pkg-config --libs --cflags gtk+-2.0` -fPIC
	```
	- **复制库文件到sublime, 并将库提前加载**
	`sudo mv libsublime-imfix.so /opt/sublime_text/`
	- **修改文件/usr/bin/subl的内容**
	`sudo gedit /usr/bin/subl`
	将
	```bash
	#!/bin/sh
	exec /opt/sublime_text/sublime_text "$@"
	```
	修改为
	```bash
	#!/bin/sh
	LD_PRELOAD=/opt/sublime_text/libsublime-imfix.so exec /opt/sublime_text/sublime_text "$@"
	```
	至此，使用命令行`subl`启动sublime应该可以输入中文了。
	
	 - **修复使用桌面图标打开时无法输入中文, 修改 sublime-text.desktop**
	 `sudo gedit /usr/share/applications/sublime-text.desktop`
	 将 **[Desktop Entry]** 中的字符串
	 `Exec=/opt/sublime_text/sublime_text %F`
	 修改为
	 `Exec=bash -c "LD_PRELOAD=/opt/sublime_text/libsublime-imfix.so exec /opt/sublime_text/sublime_text %F"`
	 将 **[Desktop Action Window]** 中的字符串
	`Exec=/opt/sublime_text/sublime_text -n`
	修改为
	`Exec=bash -c "LD_PRELOAD=/opt/sublime_text/libsublime-imfix.so exec /opt/sublime_text/sublime_text -n"`
	将 **[Desktop Action Document]** 中的字符串
	`Exec=/opt/sublime_text/sublime_text --command new_file`
	修改为
	`Exec=bash -c "LD_PRELOAD=/opt/sublime_text/libsublime-imfix.so exec /opt/sublime_text/sublime_text --command new`
	
### 中文显示问题
- **中英文字体不对齐**
解决办法:
菜单 Preference->Settings->User 打开用户配置文件Preferences.sublime-settings (中文版是【首选项】→【设置–用户】), 添加 `"font_face":"微软雅黑"`,这里使用微软雅黑可以解决问题.然后Ctrll+S保存即可(默认字体Consolas).
可以探索别的字体. `“Courier New bold”` 中的bold是粗体. Consolas是微软为程序狗设置的字体,但不支持中文. `YaHei Consolas Hybrid`是整合了雅黑字体的,效果比微软雅黑好点.安装如下:
```bash
	git clone https://github.com/cypro666/yahei.consolas-font.git
	cd yahei.consolas-font
	./install.sh
```
- **字不跟随光标**
windows下可以安装IMESupport插件解决，linux下还未找到解决方案

- **中文字体显示框框**
这个是sublime text 3的bug，当Windows 个性化 显示 中的设置自定义文本大小(DPI)，大于默认的100%的时候，就会出现这个bug。 
解决方法：
在sublime text 3中，Preference, Settings-User，最后加上一行 
`"dpi_scale": 1.0 `

- **中文字体显示乱码**
安装`ConvertToUTF8`或者`GBK Encoding Support`，选择匹配项。中文字符就可以正常显示了。



  [1]: https://www.sublimetext.com/3
  [2]: https://sublime.wbond.net/installation
  [3]: https://github.com/lyfeyaj/sublime-text-imfix
  [4]: http://tmtheme-editor.herokuapp.com/#!/editor/theme/Monokai