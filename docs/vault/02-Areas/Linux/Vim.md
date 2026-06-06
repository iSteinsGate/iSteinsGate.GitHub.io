#Vim


```
set number "设置行号
syntax on "语法高亮
set nocompatible "不与vi兼容(采用vim自己的操作命令)
set autoindent "按下回车键后，下一行的缩进会自动跟上一行的缩进保持一致
set hlsearch "高粱搜索结果
"set showmode "显示命令模式
"set showcmd "显示命令
set tabstop=4 "按下tab vim现实的空格数
"括号自动补全
inoremap ( ()<ESC>i
inoremap { {}<ESC>i
inoremap [ ]]<ESC>i
inoremap < >><ESC>i
```