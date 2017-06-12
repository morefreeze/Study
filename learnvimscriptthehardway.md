learch vimscript the hard way学习笔记
===

@(Study)[vim|学习笔记]

### CH02

1. 布尔型变量可以用`:set [no]number`这样来设置，`:set number!`表示取反，`:set number?`获取当前状态（实际`:set nonumber?`也可以哟）
1. 数值型用`:set numberwidth=10`来设置，同理`:set numberwidth?`获取当前值
1. `relativenumber`或者`rnu`用来显示相对行号，当前所在行显示绝对行号，两边分别从1,2,3开始显示

### CH03

1. 注释用`"`来标记
1. 注意注释不要写在`map`后面，这样会当成命令去执行`map a dd "comment`

### CH04

1. `map`,`nmap`,`vmap`,`imap`都知道什么意思吧

<!--more-->

### CH05

1. 第四章的`map`都是会循环定义，如`:nmap dd O<esc>jddk` 这就是一死循环
1. 用`noremap`,`nnoremap`,`inoremap`,`vnoremap`来表示非递归的map
1. `unmap`取消map定义

### CH06

1. 有一些键在normal模式几乎不会用到，可以用来map，如`<space>`,`<cr>`,`<bs>`,`-`,`H`,`L`
1. vim把prefix的key叫作leader，用`:let mapleader="-"`来定义，一般会用`,`作为leader
1. vim还有种local leader，只作用于当前类型的文件，如py,html，用`:lset maplocalleader="\\"`来定义

### CH07

1. 本章教你如何更快地去更快地编辑文件（没打错），用`:nnoremap <leader>ev :vsplit $MYVIMRC<cr>`去打开vim的配置文件，ev 意味着 edit my vimrc file
1. 用`:nnoremap <leader>sv :source $MYVIMRC<cr>` sv 意味着source my vimrc file
1. 最好用`$MYVIMRC`，这样能在不同系统下兼容

### CH08

1. vim还有个特性叫“缩写”(abbreviations)，与map不同的是他只出现在插入，替换和命令模式
1. 缩写可以用于打错字的时候，如`:iabbrev waht what`
1. 缩写会替换“非关键字符”，这货是啥，用`:set iskeyword?`看一下
1. 经常会看到这样`iskeyword=@,48-57,_,192-255`，这表示关键字符是所有英文大小写字母，数字（48-57），下划线，另外一些特殊字符（192-255）
1. 你可以看`:help isfname`了解完整描述，但这货挺长的……（这个实际是vim定义哪些字符可以成为文件名，注意里面没有空格和反斜杠\）
1. 缩写另一个用处是可以快速输入用户或版权信息，如`:iabbrev @@ morefreeze@gmail.com`
1. 缩写和map的区别是map一旦发现能匹配上就转换，而缩写需要确认这是个整词（就是isfname定义的）才会替换，假如`:inoremap ssig hehehe`，如果输入Lessig，会发现后面被替换了，而换成缩写就没有问题

### CH10

1. 有几种办法可以退到normal模式，
   1. esc
   1. <c-c>
   1. <c-[>
1. 尝试`:map jk <esc>`或者`:map jj <esc>`
1. 你要蛋疼想训练你的肌肉记忆新的map，你可以`:inoremap <esc> <nop>`（训练上面的jk反应）（**注意**：这里我尝试把<esc>disable掉了，但出现了delete键失灵的情况，在插入模式按下会插入'[3~'，即使用vim wiki上的:fixdel也不管用，所以这里建议尽快训练好jk反应）

### CH11

1. `:nnoremap <buffer> <leader>x dd`这句只会作用在当前光标在的那个窗口，切换到另一个窗口这句就不好使
1. 一般是不像上面那样写<buffer>的，而是写<localleader>，这类似命名空间，假如你写插件的话，这样会防止你写了半天的map被别人覆盖掉
1. 还有`:setlocal wrap`来只对当前窗口有效
1. 如果local和普通的map都是同一个值的话，会优先执行local的map
1. localleader一般用于文件类型的插件
1. 想知道一个键是否被map可以用:verbose imap <c-h>

### CH12 Autocommands

正常如果打开一个新文件，并且马上退出，这个文件不会留在硬盘上，如果你执行`:autocmd BufNewFile * :write`则会让文件留在硬盘上

我们来解析下这条命令，BufNewFile是vim要监控的事件，*是要过滤的事件，:write要运行的命令，
命令那部分是不允许出现<cr>这种的，
事件的类型有（只是一小部分）：
- 开始编辑一个不存在的文件
- 读一个文件无论是否存在
- 切换文件的filetype设置
- 一段时间内没有按键盘上的某个键
- 进入插入模式
- 退出插入模式

`:autocmd BufNewFile *.txt :write`这是对.txt后缀的文件进行过滤，只保存这类文件
可以同时有多个事件`:autocmd BufWritePre,BufRead *.html :normal gg=G`，在预写和读的时候格式化.html文件，
提一下BufNewFile和BufRead几乎总是同时出现，
文件类型相关的例子`:autocmd FileType python nnoremap <buffer> <localleader>c I#<esc>`，当打开一个Python文件时，使用<leader>c 可以快速注释
（建议用scrooloose/nerdcommenter 插件，对各种语言都能很好地开关注释）
通过`:help autocmd-events`查看更多的事件列表

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
9. 经常会有想把复制的内容（用`y`复制，并非剪切板）用在搜索上，或其它地方，这时可以用`<c-r>`后面再跟寄存器名，比如`<c-r>0`就是刚才复制的内容，这时会自动替换成刚才复制的值

###CH20 Variable Scoping
1. `:let b:foo='bar'`，设置变量作用域是当前buffer
2. `:help internal-variables`查看都有哪些变量作用域
3. 前面什么都不写，变量是当前作用域，在函数里就只到函数结束，否则就是全局，其他修饰符有`b:`（当前buffer）,`w:`（当前窗口）,`t:`（当前tab）,`g:`（全局）,`l:`（当前函数内的变量）,`s:`（当前使用`:source`的vim脚本）,`a:`（函数参数，只在函数内有效，和`l:`的区别是`l:`可以随便定义变量，而`a:`只限于函数传进来的参数）,`v:`（全局，vim预定义变量）
4. 可以用`:for k in keys(s:)`看到所有当前域下的变量

###CH21 Conditionals
1. 多行命令可以用`|`连接起来，而且要注意，除了开头要有**冒号**外，`|`后面的命令不需要有**冒号**
2. 当判断是个字符串时，会先尝试转成数字，然后再转成bool，比如"10foo"相当于10，同时也可以进行运行`"10foo"+10`，将会得出20，类似PHP的处理方法
3. `:if 1 | echo 'true' | elseif 2 | echo '2' | else | echo '3' | endif`

###CH22 Comparisons
1. 如果`:set ignorecase`，则`:if 'foo'=='FOO'`就是真了
2. 这个故事告诉我们写脚本时要假设用户的各种设置，不要相信裸的`==`，而要用`==?`表示大小写不敏感的相等，用`==#`大小敏感的相等，其实大部分比较都支持这个，比如`!=#`,`>=?`
3. 还有种`smartcase`会在模式串都是小写的情况下，认为是大小写不敏感，其他情况认为是敏感的
4. `\c`出现在模式串的任何位置都表示这个串是大小写不敏感，`\C`表示敏感，如`\cfoo`

###CH23 Functions
1. vim的函数都要**首字母大写**
2. 使用`:function Meow() | xxxx | endfunction`定义一个函数
3. 使用`:call Meow()`调用一个函数
4. 一个函数最多有**20**个参数
5. range函数会对选中内容的每一行执行一次，如果函数自己能处理，`:function Count() range`，则只会调用一次

###CH24 Function Arguments
1. vim的函数可以接受参数哟，在函数内使用`a:xxx`来使用
2. 函数可以接受变长参数列表，用`:function Varg(...)`，参数值是`a:0`这是参数个数,`a:1`这才是第一个参数,`a:000`这是显示**可变**参数列表（注意如果是`:function Varg2(foo, ...)`的形式，`a:000`只会显示...的参数内容），`a:1 == a:000[0]`
3. 参数`a:xxx`是**只读**变量，试图赋值会报错

###CH25 Numbers
1. vim的数字有2种，一种是有符号32位整数，一种是单浮点数
2. `echo 0x10 017`
3. `echo 5.45e+3`
4. `echo 3/2`整除，`echo 3/2.0`浮点数

###CH26 Strings
1. `:echom 'Hello' + ' world`会显示0，因为vim的+只对数字有用，这里字符串都转成了数字
2. `:echo 10 + '10.10'会显示20，因为'10.10'是个字符串会转成10
3. 字符串连接用`.`
4. `:echo 10.10 . 'hehe'`会报错，因为vim很愿意把字符串转成浮点，但反之不行
5. 转义符是`\`
6. 如果用`:echom abc\nefg`，'\n'会显示成'^@'，这是表示新换行的另一种形式
7. 用单引号表示不进行转义，`'That''s enough'`会显示成"That's enough"，两个单引号''是这里唯一的转义，表示一个字面的单引号
8. 通过`:help expr-quote`可以知道，`\x`,`\001`这种就不说了，`\u02a4`或`\U02a4`表示**当前编码**（`encoding`）下的字符值，`\b`表示删除，`\e`表示<esc>，`\f`表示"formfeed"(<FF>)
9. 用`<c-v>`或`<c-q>`来插入非数字的字面值，比如Tab，比如`<c-v>065`就是插入'A'
10. 一般模式匹配的时候用单引号比较好，省去转义\的麻烦，例如`a =~ '\s*'`

###CH27 String Functions
1. `strlen`,`len`测字符串的长度
2. `split(expr[, patt[, keepempty]])`会把字符串按patt切分成List类型，如果patt是`xxxx\zs`则会保留被切掉的部分，keepempty表示前几部分可以是空，如果不写默认是0，则返回的第一个元素一定是有值的，join(List, glue)会用glue把List粘成一个字符串
3. `tolower`,`toupper`转换大小写

###CH28 Execute
1. `execute`就是为了给字符串估值，然后去执行，比如`:execute "vsplit " . bufname('#')"`
2. vim的`execute`不像其他语言的`eval`那样危险，因为vim很少接受用户输入，即使接受也是当前用户，反正那是他们的电脑，第二个原因是vim有一些难懂的语法，但`execute`是最简单的，而且能把多行弄成一行写，见下例子
3. `execute`通常用来追加那些不能用|追加的命令，比如`:execute '!ls' | echo 'abc'`
4. 也可用于要打`:normal`的情况，如`:execute  "normal ixxx\<esc>"`，效果是切换到插入模式，输出"xxx"然后按<esc>

###CH29 Normal
1. 用`:normal G`就相当于在普通模式下执行`G`跳到文件最后
2. `:normal`也会忠实地使用任何能用的map，如果`:nnoremap G dd`，则`:normal G`会相当于删除一行
3. 可以用`:normal!`来执行原来的命令的意思
4. 但是在`:normal!`无法识别特殊字符，如`<cr>`，会当成小于号字符串cr和大于号
5. `:helpgrep`是跳到与后面模式匹配第一个的帮助上，可以用`:cwindow`或`:cnext`来切换，`:helpgrep uganda\c`忽略大小写

###CH30 Execute Normal!
1. 使用`:execute normal! gg/foo\<cr>`就可以接受特殊字符了！

###CH31 Basic Regular Expressions
1. `/`和`?`，前者向后找，后者向前找

###CH32 Case Study: Grep Operator, Part One
1. `:nnoremap <leader>g :grep -R <cWORD> .<cr>`可以搜索`<cWORD>`，表示光标下的单词（包括连字符，比`<cword>`更大），之后可以用`:cwindow`查看`quickfix`窗口
2. 以上还有一点要修改，如果光标在一个"foo;ls"下，使用后实际会执行`ls`命令，原理和SQL注入类似，所以需要用单引号保证字面值，`:nnoremap <leader>g :grep -R '<cWORD>' .<cr>`
3. 但上面对于光标有单引号的不启作用，用`:echom shellescape(expand("<cWORD>"))`可以显示shellescape后的值
4. `:nnoremap <leader>g :silent execute "grep! -R " . shellescape(expand("<cWORD>")) . " ."<cr>:copen<cr>` 最终版本，前面加个`:silent`可以防止搜索时的输出，最后再`:copen`打开结果页

###CH33 Case Study: Grep Operator, Part Two
1. 进入.vim/plugin/下，新建一个文件"grep-operator.vim"，写上`nnoremap <leader>g :set operatorfunc=GrepOperator<cr>g@`和`function! GrepOperator(type) | echom "Test" | endfunction`，这时用`<leader>giw`会发现打出了"Test"
2. 这里函数名后面有个`g@`，表示这是个"operatorfunc"，就是说后面可以跟动作，比如`iw`表示光标所在的单词，`i(`表示光标所在的圆括号，`g@`后可以跟`line`,`char`,`block`表示动作类型，这个也体现在上面函数的参数`type`上
2. 再给visual模式加个命令，加上`vnoremap <leader>g :<c-u>call GrepOperator(visualmode())<cr>`，解释下这个`<c-u>`，如果进入选择模式后，这时按`:`，会发现命令行自带了`:'<,'>`，这时如果按`<c-u>`就会“删掉从光标位置到开头的内容”，只剩`:`，正是我们想要的，这里的参数`visualmode()`是表示进的哪种visual模式，比如字节型就是`v`，行选择就是`V`，块选择就是`<c-v>`
3. 上面把函数体改成`echom a:type`后，会有几种情况，`viw<leader>g`显示`v`，`vjj<leader>g`显示`V`，`<leader>gviw`显示`char`，`<leader>gG`显示`line`

###CH34 Case Study: Grep Operator, Part Three
1. 一个可用的思路是，将选中的内容复制到临时寄存器中，但记得最后还原，如
```sh
    let saved_unnamed_register = @@
    # do something
    let @@ = saved_unnamed_register
```
1. 当你真正要写一个vim脚本时，最好使用命名空间，之前的函数改为`nnoremap <leader>g :set operatorfunc=<SID>GrepOperator<cr>g@`，这时函数名前加了`<SID>`，同时，函数定义改为`function! s:GrepOperator(type)`
2. `<SID>`应该相当于是Script ID，加上这个会在执行的时候替换为`<SNR>12_func`这种形式，另外在脚本内部使用时，前面加`s:`即可，但如果是一个map外部调用，应该使用`<SID>`

###CH35 Lists
1. vim的list下标是从0开始，用-1表示最后一个元素，-2表示倒数第二个
2. 使用l[0:2]表示切片，这里注意是表示从第0个到第2个元素，支持[-2:-1]写法，如果下界大于上界则显示空list
3. 字符串也可以进行list的操作，但要注意`"abcd"[-2]`这种只用一个负数的操作会显示为空字符串，而用`"abcd"[-2:]`才能显示为"cd"
4. `add(['a'], 'b')`将"b"插入末尾，注意这些命令在normal模式下用要写成`:call add(xxx)`
5. `extend(list, [1,2])`将两个list合成一个
5. `len(list)`求长度
6. `get(list, 0, 'default')`取list[0]，如果不存在则返回"default"
7. `index(list, 'a')`返回a在list中第一次出现的位置，没有返回-1
8. `join(list, glue)`类似php的`implode`，将list用glue连接成字符串
9. `reverse(list)`**就地**倒排list
10. `unlet list[3:]`删除list的3到最后
11. `:let l = remove(list, 3, -1)`返回删完的list
12. `filter(list, 'v:val !~ "x"')`移除元素中带"x"的，后面表达式为真将被留下
10. list用`+`号连接，也可以`+=`
11. 直接写`b = a`相当于b是a的引用，对a操作同时也会作用于b，复制可以用`b = copy(a)`，或者`b = a[:]`，但是上面这个只对一维有效，如果`a[0][1] = 'aa'`，这时会发现b[0]也变了，深度复制用`b = deepcopy(a)`（最多能复制100层）
12. 用`:echo a is b`来判断是不是同一个list，而`a == b`是从值上比较的，有个例外是，`:echo 4 == "4"`显示是1，而`:echo [4] == ["4"]`显示是0
13. 支持分别赋值，如`:let [var1, var2] = list`，如果只需要前几个值，可以这样`:let [var1, var2; rest] = mylist`，这里rest一定要有，否则会报错
14. 使用`for item in mylist`进行循环，如果是多层，则用`for [lnum, col] in [[1, 3], [2, 8], [3, 0]]`，后面的list必须每项个数一致

###CH36 Looping
1. for循环像这样
```sh
:for i in [1,2,3,4]
:  let c += i
:endfor
```
1. while循环像这样
```sh
:while c <= 4
:  let total += c
:  let c += 1
:endwhile
```
1. 有点要注意的是，当`for i in mylist`时，在对当前元素执行命令前，vim会保存下一个的引用，所以这时即使删除mylist的当前元素也是OK的，但删除之后的元素会导致vim找不到它

###CH37 Dictionaries
1. `:echo {'a': 1, 100: 'foo',}`这是定义一个字典，同时最好保留后面的逗号，防止在多行时出错
2. vim的字典只能是字符串，写成数字也会被转成字符串
3. 还可以用`:echo {}.a`的形式
4. 用`remove(dict, 'a')`或`unlet(dict.b)`来删除元素，十分推荐用`remove`，因为它的作用比`unlet`大
5. 可以用`:for key in keys(mydict)`来循环得到key值，同理，`:for v in values(mydict)`循环value，还可以`:for [key, value] in items(mydict)`两个都循环
6. 可以用`has_key()`验证一个key是不是在字典中
7. 字典的函数和上面list的函数类似

###CH38 Toggling
1. 之前学过用`:setlocal nu!`是可以开头某个布尔值的，下面来看下对于非布尔怎么做
2. 比如可以检测值满足某个条件A时，设为x，满足条件B时，设为y这样，例如
```sh
nnoremap <leader>f :call FoldColumnToggle()<cr>
function! FoldColumnToggle()
    if &foldcolumn
        setlocal foldcolumn=0
    else
        setlocal foldcolumn=4
    endif
endfunction
```
1. 或者打开或关闭某个窗口，如`:copen`和`:cclose`，这时可以用全局变量标识
```sh
nnoremap <leader>q :call QuickfixToggle()<cr>
function! QuickfixToggle()
    if g:quickfix_is_open
        cclose
        let g:quickfix_is_open = 0
    else
        copen
        let g:quickfix_is_open = 1
    endif
endfunction
```
这里只有一点不太完美，如果用户手动打开了窗口，则全局变量是不会更新的，但这里要花大工夫来完成，得不偿失
1. 这里还有一点是在打开多个文件再使用`<leader>q`时，如果在quick-fix窗口中使用，则会返回上一个split的窗口，而不是之前`copen`时所在的窗口，所以要做以下修改，同时这也是个约定俗成的写法
```sh
nnoremap <leader>q :call QuickfixToggle()<cr>
let g:quickfix_is_open = 0
function! QuickfixToggle()
    if g:quickfix_is_open
        cclose
        let g:quickfix_is_open = 0
        execute g:quickfix_return_to_window . "wincmd w"
    else
        let g:quickfix_return_to_window = winnr()
        copen
        let g:quickfix_is_open = 1
    endif
endfunction
```


###CH39 Functional Programming
1. 就是你可以把变量赋成函数，然后变量名就相当于函数名去调用，这里要注意一点是，变量可以不像函数名那样遵守首字母大写的约定，如
```sh
:let funcs = [function("Append"), function("Pop")]
:echo funcs[1](['a', 'b', 'c'], 1)
```
1. 再来感受一个类似py中的map的例子
```sh
function! Mapped(fn, l)
    let new_list = deepcopy(a:l)
    call map(new_list, string(a:fn) . '(v:val)')
    return new_list
endfunction
```
相当于对l数组中的所有元素过一遍函数fn

###CH40 Paths
1. 为啥这章和上章函数式编程没有一点关系了……
2. 获取绝对路径，`:echom expand('%:p')`或`:echom fnamemodify('foo.txt', ':p')`，但第二个必须手写foo.txt（可以是个不存在的文件）
3. 列出当前文件夹下的文件`:echo globpath('.', '*')`，还可以递归地显示文件`:echo split(globpath('.', '**'), '\n')`，递归地查找某个文件`:echo split(globpath('.', '**/*.py'), '\n')`
4. `expand(expr[, nosuf[, list]])`，expr可以以
  - '%' 当前文件
  - '#' 另一个文件
  - '#n' 另n个文件
后面还可以接修饰符
  - ':p' 扩展成全路径
  - ':h' 会把最后一部分去掉（这个可以接在一些修饰符后面，比如':p:h'就相当于全路径的文件夹名，如果只用':h'可能只显示'.'）
  - ':t' 只保留路径最后一部分
  - ':r' 移除扩展名的全路径
  - ':e' 文件扩展名
1. `simplify(filename)` 尽可能简化路径，会把'.././/'这种简化掉，快捷方式或链接不解析
2. `resolve(filename)` 可以解析快捷方式或链接，并返回简化后的全路径
3. 关于通配符，可以有`?`,`*`,`**`,`[abc]`，这里注意在win下比较蛋疼，如果要实际匹配字面值"path[abc]"写`path\[abc]`会认为反斜杠是目录分隔符，避免这样，可以写成`path\[[]abc]`

###CH41 Creating a Full Plugin
1. 看到这里你可以停了，因为前面的姿势足够你完善自己的`~/.vimrc`脚本，去修复别人脚本的bug了，绝无讽刺的意思
2. 往下学之前，建议先玩下[Potion](http://fogus.github.io/potion/index.html)语言，这是个很小的语言，使用它的目的是为了辅助我们写vim script

###CH42 Plugin Layout in the Dark Ages
1. `~/.vim/colors/`在这里的文件记录了vim的颜色主题，如果运行`:color xxx`就能看`~/.vim/colors/xxx.vim`的配色方案了，查看当前配色都有哪些用`:hi`
2. `~/.vim/plugin/`在这里的文件每次vim启动都会运行一次
3. `~/.vim/ftdetect/`这里的文件每次启动也会运行一次，`ft`的意思是`filetype`，这里的文件应该是包含`autocmd`用来切换filetype的，所以一般就**一行**
4. `~/.vim/ftplugin/`这里的名字**非常重要**，如果你`:set filetype=derp`，则会找`~/.vim/ftplugin/derp.vim`，有就运行它，同时也支持目录，也会在`~/.vim/ftplugin/derp/`下找，这样有利于分组，注意：因为只针对ft作的修改，所以确保只设置了**local-buffer**而不要是全局的！（使用`:setlocal`）
5. `~/.vim/indent/`这里的文件名规律同`ftplugin`，同样这里也只能设置**local-buffer**，这个实际和`ftplugin`没啥区别，但分开放有利于理解
6. `~/.vim/compiler/`这个和`indent`又非常相像，只是它做的是编译器相关的
7. `~/.vim/after/`这个是在`~/.vim/plugin/`启动后启动，这个作用就是有的插件为了防止和其他插件的快捷键冲突又重定义一下
8. `~/.vim/autoload/`这个比较复杂了，后面会讲
9. `~/.vim/doc/`放插件文档的地方

###CH43 A New Hope: Plugin Layout with Pathogen
1. 上面是以前的vim文件布局，新装插件需要手动解压然后BLABLA，如果作者删个文件你咋办？如果有个脚本（比如在autoload下）重名你是不是SB了？现在我们有了[Pthogen](http://www.vim.org/scripts/script.php?script_id=2332)简直就是屌
2. vim有个变量`runtimepath`，在搜索文件的时候也会去这里找，比如`syntax/`下的文件
3. Pathogen会自动将`~/.vim/bundle/`下的目录加进`runtimepath`里，在这里的每个目录下都会包含一些vim的目录，比如`colors/`，如果你在这下面用了版本控制，比如Git,那你直接`git pull orgin master`就行了啊

###CH44 Detecting Filetypes
1. 我们是要针对一种编程语言`potion`进行一些插件设置，首先新建一个`~/.vim/ftdetect/potion.vim`，输入`au BufNewFile,BufRead *.pn set filetype=potion`，这就告诉vim对于*.pn的文件，设置`filetype=potion`
2. 可以`:set filetype=c.doxygen`，点号用来分隔两种文件类型，会先用c，再用doxygen
3. 一般会用`:setfiletype`而不是`:set filetype`，因为前者设完后会只载入一次插件或语法文件

###CH45 Basic Syntax Highlighting
1. 新建一个语法文件`~/.vim/syntax/potion.vom`
```sh
if exists("b:current_syntax")
    finish
endif
echom "Our syntax highlighting code will go here."
let b:current_syntax = "potion"
```
注意用`b:current_syntax`保证互斥，不会被载入两次
2. 加入
```sh
syntax keyword potionKeyword to times
highlight link potionKeyword Keyword
```
定义`to`,`times`是关键词，然后连接potion关键词到关键词组（这个XX组就是决定了一套配色，比如函数是蓝色）
3. 上面`syntax keyword {group-name} [{options}] {keyword} ... [{options}]`，注意syntax后的keyword是固定的，接着是自定义的组名，再接着是关键词，关键词要满足`iskeyword`

###CH46 Advanced Syntax Highlighting
1. 由于一般注释都是用`#`，而`#`又不是`iskeyword`，所以我们要用正则去匹配及它后面的内容
```sh
syntax match potionComment "\v#.*$"
highlight link potionComment Comment
```
`\v`表示"very magic"，对于|()这种不需要加"\"转义了，这里还要注意`syntax match`是不支持放在组里的
1. `hi link`的位置可以任意放，放匹配前面也可以
2. 优先级是先按最后匹配的来，然后`Keyword`大于`Match`和`Region`，最后匹配开始较前的优先于匹配开始较靠后的
3. 匹配的两端必须保证是一样的字符，比如可以是+"+

###CH47 Even More Advanced Syntax Highlighting
1. 想学习更高端的语法文件配置是继续读`:h syntax`或者看别人写的
2. 这次我们用`syntax region`来匹配字符串
```sh
syntax region potionString start=/\v"/ skip=/\v\\./ end=/\v"/
highlight link potionString String
```
`skip`是用来跳过字符串中所有以反斜杠和一个任意字符的（当然也包括引号），其实这里用`skip=/\v\\"/`也可以，但应该是为了兼顾单引号的情况（这样就不用改skip了）就写成`\\.`了，比如`"She said: \"Vimscript is tricky, but useful\"!"`，

###CH48 Basic Folding
1. 手动折叠，保存在内存中，关了就没了，你可以用`:mkview [1|2...]`来保存，用`:loadview [1|2...]`来载入，都保存在`~/.vim/view`中，参看`:h viewdir`
2. Marker折叠，这种是文本中带有`// {{{`和`// }}}`的会自动折叠，但比如在JS里`{`和`}`也会折叠
3. Diff折叠，这个只有在对比文件中才有用
4. Expr折叠，这是最强大的自定义折叠功能，但工作量也多，下章讲
5. Indent折叠，按缩写折叠，以及一些空行
6.   
  - `zf[old]`是创建一个折叠
  - `zc[close]`是关闭所在光标折叠
  - `zo[pen]`是打开所在光标折叠
  - `zm[ore]`是进行一层折叠
  - `zr[educe]`是减少一层（即统一打开一层折叠）
  - 以上都可以配合大写使用，表示为全部做某事
1. `foldlevel`大于这个的所有折叠将被折叠，如果是0就表示关闭所有折叠（相当于执行了`zM`)，`foldlevelstart`是当你打开一个新文件时的`foldlevel`
2. `foldminlines`表示折叠后至少要展示的行数，也就是说少于等于这个行数就不折叠了，如果你仍然要折叠，那会认为更大一段要折叠，比如一个缩进只有2行，这时`:set foldminlines=2`，那这2行死活是不会折叠的，如果你在两行中按`zc`，则会将周围的上一层缩进一起折叠了

###CH49 Advanced Folding
1. 折叠规则：
  - 每一行都有一个折叠层数，这是个是>=0的数
  - ==0的行是永远不会折叠的
  - 相邻折叠层数的行会被一起折叠
  - 如果一个层数X被折叠，则所有相邻大于等于X的行都会被折叠进来
1. 你可以用`:echo foldlevel(line_num)`来显示第几行的折叠层数
2. 有个技巧是，如果你自定义的一个foldlevel函数返回了'-1'，则会告诉vim这是未定义的折叠层数，vim会使用这行上面或下面的一行中**较小**的作为这行的foldlevel
3. 如果上面函数返回`>n`，就表示这是个大于n的折叠层数
3. 一般自己定义一个按Indent的层数函数可以这样写
```sh
function! IndentLevel(lnum)
    return indent(a:lnum) / &shiftwidth
endfunction
```

###CH50 Section Movement Theory
1. 这章好像讲了`[[`,`[]`,`][`,`]]`起源于`nroff`语言（一类类似Latex的文本语言）的宏
2. `[`和`]`分别是向前或向后寻找，第二位的`[`,`]`表示寻找`{`还是`}`，在C语言里就是跳到上（或下，取决于第一位是`[`还是`]`）一个函数的开始或结尾

###CH51 Potion Section Movement
1. 这章为Potion语言打造了一个自制的section跳转

###CH52 External Commands
1. 这章实现了一个自动编译的功能，新建`~/.vim/ftplugin/potion/running.vim`
```sh
if !exists("g:potion_command")
    let g:potion_command = "potion"
endif
function! PotionCompileAndRunFile()
    silent !clear
    execute "!" . g:potion_command . " " . bufname("%")
endfunction
nnoremap <buffer> <localleader>r :call PotionCompileAndRunFile()<cr>
```
这里需要设置`g:potion_command`的值
1. 介绍下`system(expr[, input])`函数，expr是命令，input是之后输入的内容，比如有的命令支持从stdin读取数据，input就是那里的内容
2. 给那些想用vim做任何事的人看`:h design-not`
3. `:read expr`是在当前光标下插入expr文件内容，`:read !expr`是插入执行命令的结果

###CH53 Autoloading
1. 对于一些小的插件，在载入时全部载入还是OK的，但如果比较大的就会花费一些时间，这时就要延迟载入，用`:call somefile#Hello()`，有了这个的函数，vim会从`~/.vim/autoload/somefile.vim`中找这个函数，如果有多个`#`，比如`path#a#b()`会从`~/.vim/autoload/path/a.vim`中找，然后多次调用不会重新载入这个文件，所以你作了修改也没用，但如果你调用`:call somefile#BadFunction()`，那就会重新载入了，并且`#Hello()`的更新也生效了
2. 一般会把map的函数以这种命名放在autoload里，这样只有第一次使用这个map时才会load这个文件

###CH54 Documentation
1. 每个插件都最好有个文档来说明，这个文件放在`~/.vim/doc/`下，与插件名同名的**.txt**文件
2. 编辑完文件后，用`:Helptags`把这个文件加入索引，然后就可以用`:h xxx`来查看了
3. 你可以用[figlet](http://www.figlet.org/)来生成一个字符的立体画，非常酷炫
3. 哪些需要放在doc中说明呢
  - Introduction 这是放整个插件的概览
  - Usage 告诉用户应该怎么用，比如有哪些map，如果mapping太多，建议你单独开一个`Mappings`章节来放
  - Mappings 见上
  - Configuration 这里应该列出所有需要用户修改的值，及它的**效果**和**默认值**
  - License 只要包含license的url就行，千万别包含整个文本
  - Bugs 这里只记录主要的bugs，并且告诉用户怎么找到你报告bug
  - Contributing 如果你期望用户可以帮你修复bug，明确告诉用户他应当怎么做去修复bug，是发pull request？还是发邮件？
  - Changelog 记录版本X到版本Y的变化过程，十分建议你按照[Semantic Versioning](http://semver.org/)去定义你的版本
  - Credits 这里可以包含自己的名字，以及一些给你启发的脚本名，贡献者们
1. 帮助文档中，一排的`=`可以分隔章节，`-`分隔小章节，用`*xxx*`来生成tag，这样能被help索引到，用`|`包围的单词是一个跳转的链接，在这些词上按`<c-]>`可以跳到对应的tag上，用单引号`'`来表示一个选项名，用波浪号放在一行结果，`Heading~`将会高亮整行，用`> xxxx \n yyyy <`表示一段代码，中间可以换行，还有些自动高亮的单词，如`Todo`,`Error`是`~/.vim/syntax/help.vim`下配置的
2. 有些好的例子可以参考下
  - [Clam](https://github.com/sjl/clam.vim/blob/master/doc/clam.txt) 一个简单的例子
  - [NERD_tree](https://github.com/scrooloose/nerdtree/blob/master/doc/NERD_tree.txt) 观察它如何组织mapping
  - [Surround](https://github.com/tpope/vim-surround/blob/master/doc/surround.txt)
  - [Splice](https://github.com/sjl/splice.vim/blob/master/doc/splice.txt) 这里用了ASCII画的形式来描述
1. 可以使用`:left`,`:center`,`:right`来达到左中右对齐的效果

###CH55 Distribution
1. 将你的插件放在网上，比如[the scripts section of the Vim websit](http://www.vim.org/scripts/)，但现在大多放在GitHub上，也方便使用Pathogen管理
2. 关注reddit上的[/r/vim](http://www.reddit.com/r/vim/)版块

###CH56 What Now?
1. 以下内容在你闲得蛋疼可以看
  - `:h highlight` 你可以发现一些内置的配色方案，可以用在之后的插件中
  - `:h user-commands` 学习怎样创建一个你自己的Ex命令，类似`:command`
  - `:h ins-completion` 这就是插入模式下的自动补全啊，进阶阅读`:h omnifunc`和`:h compl-omni`（这里原作者打成`:h coml-omni`了）
  - `:h quickfix.txt` vim还可以和编译器进一步地互动，阅读这个吧
  - `:h Python` `:h Ruby` `:h Lua` vim居然还支持这些语言的接口
  - 以下是些更有趣的内容
    - `:help various-motions`
    - `:help sign-support`
    - `:help virtualedit`
    - `:help map-alt-keys`
    - `:help error-messages`
    - `:help development`
    - `:help tips`
    - `:help 24.8`
    - `:help 24.9`
    - `:help usr_12.txt`
    - `:help usr_26.txt`
    - `:help usr_32.txt`
    - `:help usr_42.txt`


非常感谢GB大牛推荐我阅读这本书！
