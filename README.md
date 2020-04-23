# musicpy
你们想过用代码来写音乐吗？

最近几个月学业繁忙，但是业余时间自己开发了很多python库，内容包括数学统计，各种游戏，还有音乐等等。其实还有试着写AI方面的，但是目前还是初期进度。今天我想先介绍一下我正在开发中的一个音乐代码语言库: musicpy。

这个库可以让你用非常简洁的语法，来表达一段音乐的音符，节奏等等信息，并且可以简单地输出成midi文件的格式。这个库里面涉及到非常多的乐理知识，所以个人推荐至少要先了解一部分乐理再来使用会比较上手。相对地，如果你是一个对乐理比较了解的人，那么看完我写的教程之后你应该很快就上手了。

在musicpy里面，几个基本的类是note（音符）, chord（和弦）和scale（音阶）。这几个类是构成音乐代码的基础。musicpy里面的乐理功能非常多，先从几个最基本的开始介绍吧。

1、note类（音符类），初始化一个note的实例只需要给一个音名（CDEFGAB其中一个）和一个音高（一个正整数），现在你就可以使用这个音符去做音乐里的任何事情了。你还可以设定音符的duration（音符长度）和volume（音符的音量大小）。音符长度默认为1，音量默认为100。

比如： a = note('A', 5)

这样就是把音符A5赋值给了a。

2、 接下来需要介绍使用musicpy里面的一个函数叫做“play”, play函数可以播放任何符合musicpy正确语法的语句，并且同时生成midi文件。play函数的第一个参数是音乐语句，之后几个比较重要的关键字参数是tempo（速度），name（midi文件的名字），modes（可以选择重新写一段音乐或者往之前的音乐之后追加等等）。比如之前的音符a，只要运行play(a)，就可以听到音符A5的钢琴声音了。

3、chord类（和弦类），这个应该是最重要的类了。在musicpy里，和弦类被定义为“一组音符的集合”，这个定义或许比乐理里面的和弦定义更为广义化，因为按照这个定义，一首完整的乐曲也可以完全装进和弦类里面，在这个库里也确实可以2333

初始化一个和弦实例，只需要给一个音符的列表即可。还可以设定duration（所有音符的长度设置），interval（音符之间的间隔，用列表表示）。这里比较人性化的一个地方是，你在给定音符列表时无需先用note类初始化，而只需要直接写音符的名字（字符串）就行了。

比如： Cmaj7 = chord(['C5', 'E5', 'G5', 'B5'])

这样就写出了一个C大七和弦了。我们可以用play函数播放这个和弦：

play(Cmaj7)

4、 接下来介绍一个很方便的函数：getchord，这个函数可以让你得到你想要的类型的和弦，只需给定和弦的根音和和弦类型即可。由于这个函数的名字相对有点长，所以用chd这个名字（chord的缩写）也可以调用getchord。比如：

Am7 = chd('A', 'm7')，这样我们就创建了一个A的小七和弦。和弦类型的种类很多，具体可以到database.py这个文件里面的chordTypes查看，用户还可以自己添加自己喜欢的和弦种类，只需按照chordTypes里相同的格式，写上和弦名称和对应的和弦音程即可。

5、接下来是关于和弦类的几个比较重要的内置方法的介绍。

1) inversion可以得到一个和弦的转位，里面有一个参数num代表和弦的第几转位。

比如：chd('A', 'm7').inversion(1) 可以得到A的小七和弦的第一转位。

2) 如果想对两个和弦（比如a和b）进行拼接，那么只需要a + b即可。

如果想对和弦A添加一个音符x，那么只需要A + x即可。

如果想去掉和弦A里面的某一个音x，那么只需要 A - x即可。

如果想重复一个和弦A重复n次，那么只需要 A * n即可。

如果想把一个和弦A里面的音符倒序排列，那么只需要A.reverse()即可。

注意这里的和弦A不一定只是乐理上的一个和弦，而是可以是一段旋律，甚至是一段旋律加上一段和弦铺底。因为musicpy里的和弦类定义就是“一串音符的集合”。

3) intervalof函数可以返回一个和弦的构成音之间的音程关系。参数cummulative设为True的时候返回和弦所有的的构成音（除根音外）和根音之间的音程，设为False的时候返回和弦从最低音到最高音两两之间的音程关系。默认值为True。比如：

chd('C','maj').intervalof()

会得到 [4,7]，这个表示C大三和弦（C,E,G）里面第二个音到根音之间有四个半音，第三个音到根音之间有7个半音。如果你想看音程在乐理上的名称，那么可以设参数translate为True，那么你就可以看到对应的音程名称了。比如：

chd('C','maj').intervalof(translate = True)

会得到 ['major third', 'perfect fifth']，也就是大三度和纯五度。

4) 如果你想对一个和弦（当然也可能是一段旋律或者音乐片段）升调或者降调，那么可以用up和down函数。给定一个和弦A，A.up(x)表示把和弦A整体升高x个半音，A.down(x)表示把和弦A整体降低x个半音。如果你只想把和弦里的某个音升高，那么只需要A.up(x, k)，降低则是A.down(x, k)，其中k表示第几个音。比如chd('C','maj').up(1)会得到一个新的和弦，里面每个音都比之前的和弦升高一个半音，构成音为C#, F, G#。chd('C','maj').up(1,1)会得到一个新的和弦，只有第一个音升高一个半音，构成音为C#, E, G。

5) 如果你想对一个和弦A的组成音进行排序，那么只需要A.sort(x)，其中x为一个记载顺序的列表，比如有一个A的小七和弦，对于它的4个组成音A,C,E,G你想按照CEAG的顺序排列，那么就写A.sort([2, 3, 1, 4])即可。

6) 如果你想对一段旋律（包括和弦铺底）进行转调，那么可以使用modulation函数。比如现在想给一个音乐片段A转调，那么就可以A.moulation(之前的调， 要转向的调)，关于如何表示一个调式，这个接下来会谈。

7) 如果想对一段旋律（或者和弦）A截取其中的一部分，那么只需要A[开始位置:结束位置]即可。

8) 如果想得到和弦或者旋律A的所有音符名称，就写A.names()即可。



6、scale类（音阶类），这个类可以表示一个特定的音阶。使用这个类可以快速按照音的间隔来构建调式，比如大调的音的排列是全全半全全全半（全代表全音，半代表半音），那么如果想构建一个C大调音阶，就可以写scale('C5', interval = [2,2,1,2,2,2,1])，这样就得到了以C5为根音的C大调音阶。当然，对于大部分知名的调式来说，只需要输入调式的名称就行了。比如scale('C5', 'major')就可以得到以C5为根音的C大调音阶，scale('C5', 'minor')得到以C5为根音的C小调音阶。在database.py里面的scaleTypes是所有musicpy自带的调式，用户也可以自己定制调式。

7、转调的具体实例：

比如现在有一个音乐片段p是A大调，现在想转到A小调，那么就可以写:

p.modulation(scale('A', 'major'), scale('A', 'minor'))

这样就把音乐片段p从A大调转到A小调啦~

8、关于音阶类的几个重要的内置方法的介绍：

1) 如果你想按照音阶的级数选取对应的和弦，比如C大调的4级三和弦是F，6级三和弦是Am，这时候可以用pickchord_by_degree函数，其中的参数degree1是和弦级数，num是选取多少个音，step是每一次跨越几个音阶中的音。

按照默认值，scale('C', 'major').pickchord_by_degree(5)会得到一个G大三和弦。

2) fifth函数可以按照五度圈将当前的调式进行转调，其中的参数step是顺着五度圈移动多少步，如果step大于0，那么就会顺时针转调，如果step小于0则是逆时针转调。fourth函数同理，只是换成了四度圈。

3) 音阶类有内置一组较为完整的调式和弦选取函数。比如现在有一个调式A。那么调式A的主和弦就是A.tonic_chord()，下属和弦就是A.subdom_chord()，属和弦是A.dom_chord()等等。想得到调式内某一级的副属和弦可以用A.secondary_dom(degree)，其中degree是级数。

4) relative_key函数可以得到关系调，比如大调的关系小调或者小调的关系大调。

parallel_key函数可以得到同主音大小调。

5) up和down函数也同样适用于音阶。

9、这个库也可以读取midi文件，并且自动转换为chord类，然后就可以进行各种各样的编辑了。使用read函数可以读取一个midi文件，write函数可以把音乐代码写入midi文件。

10、这个库里面其中一个非常好用的功能就是可以判断任意的一组音是什么和弦。使用detect函数即可，比如detect(x)，其中x只需要是一个包含音符名称的列表，返回的是和弦名称。除了原位和弦之外，各种复杂的和弦转位，省略，变化音，voicing等等都可以判断。由于一组音判断为和弦常常有不止一种的解释法，尤其是当音比较多的时候，因此detect函数有很多参数可以调节解释和弦判断的优先级。detect函数可以判断很复杂的和弦组成。

举例： detect(['C', 'E', 'G#', 'D'])得到的是Eaug7/C，detect(['C','A','E'])得到的是Aminor/C。

关于这个音乐代码库，还有很多功能可以讲，不过这些就留到下一期专栏了qwq

这里给大家附上一个音乐代码示例，
东方主旋律：

play((getchord_by_interval('D#5',[5,7,10,7,5],2,0.5)*3+getchord_by_interval('F5',[1,0,-4],2,0.5))*3,150)

接下来准备按照专栏的内容做几期介绍视频，会分成多个部分来介绍。这个库有在我的github上，并且不断更新中，不过目前还没准备开源。（注释部分还有很多没写的qwq）

我开发这个语言主要的初衷有两点，第一，比起工程文件和midi文件单纯存储音符，力度，速度等单位化的信息，如果能够按照乐理上的角度来表示一段音乐从作曲上的角度是如何实现的，那就更加有表示的意义了。而且只要不是现代主义无调性音乐，大部分的音乐都是极其具有乐理上的规律性的，这些规律抽象成乐理逻辑语句可以大大地精简化。（比如一个midi文件1000个音符，实际上按照乐理角度可能可以简化到几句代码）。第二，开发这个语言是为了让作曲AI能够在真正懂得乐理的情况下来作曲（而不是深度学习，喂大量的数据），这个语言也算是一个接口，AI只要把乐理的语法搞懂了，那作曲就会拥有和人一样的思维。