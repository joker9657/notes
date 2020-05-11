今天刚好准备把笔记扔上 GitHub 备份，查看 `git status` 的时候发现中文乱码了，大概长下面这样子。
```
$ git status
位于分支 master
您的分支与上游分支 'origin/master' 一致。

要提交的变更：
  （使用 "git restore --staged <文件>..." 以取消暂存）
	新文件：   "Git Flow\345\267\245\344\275\234\346\265\201\347\250\213.md"
	新文件：   "Laravel \345\244\232\345\257\271\345\244\232\345\205\263\350\201\224\346\237\245\350\257\242.md"
	
$ git config --global core.quotepath false // 这里中文被转义了，配置为 false 即可
```
> core.quotePath
Commands that output paths (e.g. ls-files, diff), will quote "unusual" characters in the pathname by enclosing the pathname in double-quotes and escaping those characters with backslashes in the same way C escapes control characters (e.g. \t for TAB, \n for LF, \\ for backslash) or bytes with values larger than 0x80 (e.g. octal \302\265 for "micro" in UTF-8). If this variable is set to false, bytes higher than 0x80 are not considered "unusual" any more. Double-quotes, backslash and control characters are always escaped regardless of the setting of this variable. A simple space character is not considered "unusual". Many commands can output pathnames completely verbatim using the -z option. The default value is true.


