learch vimscript the hard way学习笔记
===

###CH02

1. 布尔型变量可以用`:set [no]number`这样来设置，`:set number!`表示取反，`:set number?`获取当前状态（实际`:set nonumber?`也可以哟）
1. 数值型用`:set numberwidth=10`来设置，同理`:set numberwidth?`获取当前值
2. `relativenumber`或者`rnu`用来显示相对行号，当前所在行显示绝对行号，两边分别从1,2,3开始显示

###CH03
2. 注意注释不要写在map后面，这样会当成命令去执行`map a dd "comment`

###CH04
1. `map`,`nmap`,`vmap`,`imap`都知道什么意思吧

###CH05
1. 第四章的`map`都是会循环定义，如`:nmap dd O<esc>jddk` 这就是一死循环
2. 用`noremap`,`nnoremap`,`inoremap`,`vnoremap`来表示非递归的map
3. `unmap`取消map定义

###CH06
1. 有一些键在normal模式几乎不会用到，可以用来map，如`<space>`,`<cr>`,`<bs>`,`-`,`H`,`L`
2. vim把prefix的key叫作leader，用`:let mapleader="-"`来定义，一般会用","作为leader
3. vim还有种local leader，只作用于当前类型的文件，如py,html，用`:lset maplocalleader="\\"`来定义

###CH07
1. 本章教你如何更快地去更快地编辑文件（没打错），用`:nnoremap <leader>ev :vsplit $MYVIMRC<cr>`去打开vim的配置文件，_ev_意味着**e**dit my **v**imrc file
2. 用`:nnoremap <leader>sv :source $MYVIMRC<cr>` _sv_意味着**s**ource my **v**imrc file
3. 最好用`$MYVIMRC`，这样能在不同系统下兼容

###CH08
1. vim还有个特性叫“缩写”(abbreviations)，与`map`不同的是他只出现在插入，替换和命令模式
2. 缩写可以用于打错字的时候，如`:iabbrev waht what`
3. 缩写会替换“非关键字符”，这货是啥，用`:set iskeyword?`看一下
4. 经常会看到这样`iskeyword=@,48-57,_,192-255`，这表示关键字符是所有英文大小写字母，数字（48-57），下划线，另外一些特殊字符（192-255）
5. 你可以看`:help isfname`了解完整描述，但它货挺长的……（这个实际是vim定义哪些字符可以成为文件名，注意里面没有空格和反斜杠\）
6. 缩写另一个用处是可以快速输入用户或版权信息，如`:iabbrev @@ morefreeze@gmail.com`
7. 缩写和`map`的区别是map一旦发现能匹配上就转换，而缩写需要确认这是个整词（就是isfname定义的）才会替换，假如`:inoremap ssig hehehe`，如果输入Lessig，会发现后面被替换了，而换成缩写就没有问题

###CH09
8. 这章最后的练习题让你实现个对选中文字两端加单引号的map，注意这里按完`v`选完文字后，要再按下`v`或者`<esc>`确定选完，这时选中的内容实际已经被存起来了，再按`` `< ``就能跳到选中的文字开头了

###CH10
1. 有几种办法可以退到normal模式，
    - `esc`
    - `<c-c>`
    - `<c-[>`
1. 尝试`:map jk <esc>`或者`:map jj <esc>`
2. 你要蛋疼想训练你的肌肉记忆新的map，你可以`:inoremap <esc> <nop>`（训练上面的jk反应）**注意**：这里我尝试把`<esc>`disable掉了，但出现了delete键失灵的情况，在插入模式按下会插入'[3~'，即使用[vim wiki](http://vim.wikia.com/wiki/Backspace_and_delete_problems#Delete_key_problems)上的`:fixdel`也不管用，所以这里建议尽快训练好`jk`反应

###CH11
1. `:nnoremap <buffer> <leader>x dd`这句只会作用在当前光标在的那个窗口，切换到另一个窗口这句就不好使
2. 一般是不像上面那样写`<buffer>`的，而是写`<localleader>`，这类似命名空间，假如你写插件的话，这样会防止你写了半天的map被别人覆盖掉
3. 还有`:setlocal wrap`来只对当前窗口有效
4. 如果local和普通的map都是同一个值的话，会优先执行local的map
5. localleader一般用于文件类型的插件
6. 想知道一个键是否被map可以用`:verbose imap <c-h>`

###CH12 Autocommands
1. 正常如果打开一个新文件，并且马上退出，这个文件不会留在硬盘上，如果你执行`:autocmd BufNewFile * :write`则会让文件留在硬盘上
2. 我们来解析下这条命令，`BufNewFile`是vim要监控的事件，`*`是要过滤的事件，`:write`要运行的命令
3. 命令那部分是不允许出来`<cr>`这种的
3. 事件的类型有（只是一小部分）：
  - 开始编辑一个不存在的文件
  - 读一个文件无论是否存在
  - 切换文件的`filetype`设置
  - 一段时间内没有按键盘上的某个键
  - 进入插入模式
  - 退出插入模式
4. `:autocmd BufNewFile *.txt :write`这是对.txt后缀的文件进行过滤，只保存这类文件
5. 可以同时有多个事件`:autocmd BufWritePre,BufRead *.html :normal gg=G`，在预写和读的时候格式化.html文件
6. 提一下`BufNewFile`和`BufRead`几乎总是同时出现
7. 文件类型相关的例子`:autocmd FileType python     nnoremap <buffer> <localleader>c I#<esc>`，当打开一个Python文件时，使用`<leader>c` 可以快速注释
8. 通过`:help autocmd-events`查看更多的事件列表
9. 在做习题时，发现可以`map <buffer> <leader> yyyy`而不能`map <buffer> <localleader> yyyy`，注意这里需要先设置localleader，我猜你就没设`:let localleader="\\"`

###CH13 Buffer-Local Abbreviations
1. 这章就是说`iabbrev`也能用`<buffer>`来修饰
2. 你想记住某个新的snippet最好办法就是disable掉原来的命令，比如`iabbrev <buffer> return NOPENOPENOPE`

###CH14 Autocommand Groups
1. `autocmd`是不会替换原先的命令的，假如使用两次同样的命令，那触发autocmd时会进行两次命令
2. 特别要注意在你`source $MYVIMRC`时，autocmd会再载入一次！
3. 可以用`augroup testgroup autocmd xxx augroup END`，这时如果你运行下`augroup testgroup autocmd yyy augroup END`，你猜`xxx`还会执行吗？耶，答案是会！
4. 你可以在augroup里用autocmd!来清空augroup

###CH15 Operator-Pending Mappings
1. vim有一类命令后面可以接动作，比如`d`,`y`,`c`
2. `onoremap p i(`，如果在普通模式下输入`dp`，相当于`di(`，就是把括号内的内容删掉
3. `:onoremap b /return<cr>`，输入`db`，会向下删除，直到遇到**return**为止
4. `:onoremap in( :<c-u>normal! f(vi(<cr>`，输入`cin(`会直接将光标移到一个括号，并且删除括号内的内容，然后变成插入模式，`<c-u>`之后会讲，这里没什么用，只是保证能在所有情况下运行，`normal! dddd`表示在普通模式下按`dddd`（删除2行），`f(`表示前进到最近的左括号，`vi(`表示选中括号内的内容，别忘最前面是`c`，所以相当于改变括号内的内容
5. 定义延迟map有两个规则：
  - 如果你定义的内容是一段选中的内容，则vim会直接操作这段内容
  - 否则vim会操作原光标到到新位置的内容
1. `<c-u>`可以通过`:help omap-info`查看，但**没懂**，用于移除vim可能插入的范围（"The CTRL-U (<C-U>) is used to remove the range that Vim may insert."）

###CH16 More Operator-Pending Mappings
1. 这章讲了更复杂的延迟map的例子，比如`:onoremap ih :<c-u>execute "normal! ?^==\\+$\r:nohlsearch\rkvg_"<cr>`，在Markdown里，当光标在内容中时，会选中heading
2. `:normal gg`相当于普通模式下执行`gg`，跳到文件头
3. `:execute "normal! gg"`和上面是一个效果
4. `:normal! gg/a<cr>`就不是你想的那样，因为normal无法识别特别字符，比如`<cr>`，但是，你可以用`:execute "normal! gg/a\r"`来完成，注意，将"<cr>"替换成了"\r"
5. 另一个值得注意的地方是，第1条给出的命令最后用了`g_`而不是`$`，他俩都是到当前行最后一个字符，但`$`会多匹配一个，你感受一下
6. `normal!`中的感叹号是表示不使用map
7. `:help normal`,`:help execute`,`:help expr_quote`也许会有用

###CH17 Status Lines
1. `:set statusline=%f`设置状态栏为显示文件名
2. 注意后面如果有空格的话，要用"\"来转义，否则会认为是set命令的下一条，因为set支持一次设置多个
3. 一次写一整行的状态栏太复杂难懂了，可以分开写，只要`:set statusline=%f`,`:set statusline+=\ -\`,...
4. `:set statusline=[%4l]`的%4表示占4个字符，不足用空格补齐，`%-4`表示左对齐，`%04`表示不足用0补齐，`%.20`表示占20个字符的宽，多的会截掉，一般格式为`%-0{minwid}.{maxwid}{item}` 

###CH18 Responsible Coding
1. 多写注释
2. 可以用`" Vimscript file settings ---------------------- {{{`和`: }}}`来包住一个想被折叠的部分，同时需要设置`:setlocal foldmethod=marker`，在这部分内容中时，按`za`来折叠或打开
3. vim有许多缩写的命令，比如`setl`==`setlocal`，但十分不建议在vim script里这么写，如果你是临时手写命令的话，可以用缩写

###CH19 Variables
1. 用`:set foo='bar'`对变量赋值
2. `:set textwidth=80`，用`:echo &textwidth`获取选项的变量值，对bool值也可以会用，如`:let &wrap=1`
3. 对选项变量的值进行赋值也相当于修改选项，如`:let &textwidth=100`,`:let &textwidth=&textwidth+10`
4. 你可以用`:let l:number=1`设置local变量
5. 用`:let @a='hello'`设置一个寄存器变量，@"是复制时的内容，@/是按`/keyword`中的keyword内容
6. bool值不可以用纯字母的字符串，如果是'42','0'这种还是可以转换成数字1或0的
7. 可用的寄存器有"-a-zA-Z0-9"（寄存器0存最近一次y的内容，1存最近一次删除或改变的内容，如果少于一行的话或指定别的寄存器，则不存，当还有删除或改变时，会将1的内容移到2，依此类推），未命名寄存器""（vim的`d`,`c`,`s`,`x`,`y`的内容，也包括上一个用名字的寄存器的内容），4个只读寄存器:.%#（":"保存上一条命令，"."保存一次输入内容，"%"当前文件全路径，"#"备用文件全路径），表达式寄存器=（这不是一个真的寄存器，只是在一些命令中表示这里是一个寄存器），选择或drop寄存器*+~（这在GUI界面才有），黑洞寄存器_（当使用`"_dd`时，将不存储dd的内容到任何寄存器），搜索寄存器，太多了，看`:help registers`吧
8. `:display`显示所有寄存器的值

###CH20 Variable Scoping
1. `:let b:foo='bar'`，设置变量作用域是当前buffer
2. `:help internal-variables`查看都有哪些变量作用域
3. 前面什么都不写，变量是当前作用域，在函数里就只到函数结束，否则就是全局，其他修饰符有`b:`（当前buffer）,`w:`（当前窗口）,`t:`（当前tab）,`g:`（全局）,`l:`（当前函数）,`s:`（当前使用`:source`的vim脚本）,`a:`（函数参数，只在函数内有效，和`l:`有区别？）,`v:`（全局，vim预定义变量）
4. 可以用`:for k in keys(s:)`看到所有当前域下的变量

###CH21 Conditionals
1. 多行命令可以用`|`连接起来，而且要注意，除了开头要有**冒号**外，`|`后面的命令不需要有**冒号**
2. 当判断是个字符串时，会先尝试转成数字，然后再转成bool，比如"10foo"相当于10，同时也可以进行运行`"10foo"+10`，将会得出20，类似PHP的处理方法
3. `:if 1 | echo 'true' | elseif 2 | echo '2' | else | echo '3' | endif`

###CH22
1.

###CH23
1.

###CH24
1.

###CH25
1.

###CH26
1.

###CH27
1.

###CH28
1.

###CH29
1.

###CH30
1.

###CH31
1.

###CH32
1.

###CH33
1.

###CH34
1.

###CH35
1.

###CH36
1.

###CH37
1.

###CH38
1.

###CH39
1.

###CH40
1.

###CH41
1.

###CH42
1.

###CH43
1.

###CH44
1.

###CH45
1.

###CH46
1.

###CH47
1.

###CH48
1.

###CH49
1.

###CH50
1.

###CH51
1.

###CH52
1.

###CH53
1.
