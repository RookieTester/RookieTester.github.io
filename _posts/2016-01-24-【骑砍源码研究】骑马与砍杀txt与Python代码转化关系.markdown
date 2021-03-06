---
layout:     post
title:      "骑马与砍杀txt与Python代码转化关系"
subtitle:   ""
date:       2016-01-24 21:46:45
author:     "小白"
tags:
    - 骑马与砍杀
    - Module System
    - 反编译
---

# 利用魔球的触发器翻译，得出兵种、部队、装备的txt码 #
首先不得不提到魔球大法啊，这真是一个实用的工具，里面的具体用法我就不多说了。这里只说如何利用魔球的触发器，翻译得到一些经常需要的txt码。

具体做法：
魔球中参考任意一个触发器新建一个新的触发器，然后将以下代码复制粘贴进去（仅供参考）。

```python
(party_add_members,"p_main_party","trp_player",1),
(spawn_around_party,"p_main_party","pt_forest_Empire_Religious2"),
(set_item_probability_in_merchandise,"itm_salt", 700),
```

这三句py已经涉及到了部队、兵种、部队模板、物品了，在触发结果的“py代码”部分将这些粘贴进去，然后切换到“伪代码”，就可以完成翻译。然后你可以通过右键选择这些元素，进行相应的更换。
![](http://imgsrc.baidu.com/forum/w%3D580/sign=2678e6a80d7b02080cc93fe952d8f25f/f882be2397dda144e54aa2bfb5b7d0a20df486d6.jpg)
# txt文件中的控制字 #
控制字这个概念还是很重要的，貌似是由吧友吾名原暗首先提出的？几乎每一个涉及功能的txt文件都少不了控制字。而且控制字还不止一种，为了方便称呼，我就叫他们大控制字和小控制字。。。

**1.大控制字（一般都在文件的第二行）：**
例如打开conversation文件，第二行会有个四位数的阿拉伯数字，那个就是大控制字，那个数字代表着读取游戏时有效的对话数。以下是我通过实验得出的推测：如果控制字为1000，加载游戏时，程序会从第一个对话读取，一直读到第1000个对话。如果对话代码没有问题，则可以进入游戏；代码有误，就会跳出。

这里可以看出控制字的重要性。
以下是两种常见却容易被忽视的问题：

a.如果你在conversation文件末尾添加了一个新对话，却没有变动大控制字的话，进入游戏后新增对话就不会生效；

b.将新增的对话添加在conversation文件的上面或中间，这样你进游戏会发现你的修改生效了，但是之前待在文件最下面的对话就没有了（但是有时放在最下面的对话并不常用，有时甚至是没用的，所以不会及时发现异常）。

**2.小控制字**

小控制字一般出现在代码段内，相比大控制字而言，比较不容易被发现。举个例子：

dlga_spouse_talk:spouse_household_relation69631 564 0 {!}like_me. 6 【1】 1 3 936748722493063440 144115188075855892 20 NO_VOICEOVER

这里用方括号包装的1即是一个小控制字。小控制字的含义是：这个代码段里的py代码行数。至于代码段，如果在conversation里就是一个完整的对话，如上就是一个代码段。

而py行数，比如例子中的对话，相应的功能对应的py就是：

```python
(call_script,"script_change_player_relation_with_troop","$g_talk_troop", 20),
```

看吧，只有一句（姑且算是一个括号，一行py吧，这样好数）。
所以例子中的小控制字就是1.至于控制字的位置嘛，这个得通过不断地修改慢慢体会（一般会是英文后的第二个或者第三个数字）。
# 好用的翻译工具 #
这里得提到魔球以外的另一个工具，MBCodeEditor。当我们接触了比较多的py语句以后，会发现有些py语句魔球翻译不了，而且会报错，其实这种情况不一定是语句有错，很可能是因为魔球不支持这句py。这种时候，这个软件的作用就大了。
如图
![](http://imgsrc.baidu.com/forum/w%3D580/sign=0ed65025902bd40742c7d3f54b889e9c/e58a86ec8a13632765220c3e968fa0ec09fac742.jpg)
先点“browse”选择mod文件夹的路径，选择好以后，点击“load mod”，然后才能进行其它操作。“output as source code”就是把指定的txt文件导出成为py，文件默认导出在mod文件夹内，和那些txt在一起。

通过阅读py文件可以进步的更快，当然不必心急，刚开始肯定会比较吃力。
不过我们开始只需要用到翻译功能就行，Module_system_code框里填写py代码，Text_codeL里填写txt代码，就可以互相翻译了。

*注意：这个反编译软件仅用来学习即可，反编译是一种侵害版权的行为，不可用于商业行为。*
# 简单的修改示例 #
正好是昨天才弄完的，过程也比较简单。

某位吧友提出要修改“对话提升与女士的关系”。

1.那么接到这个问题时，首先就要想到，修改对话，就应该改conversation.txt这个文件，这个思路一定要有，至于mod文件夹里的txt文件都对应哪些功能我稍后再讲。

2.然后问题就是，提升关系的代码语句怎么写？这里不必慌张，可以先想想游戏里是否有现存的功能符合这个要求。经常开作弊菜单的人，会发现和领主对话多出了一个选项（未汉化，全英文的一句），点进去，如图
这里的like me选项，点击以后会提示与对话者的关系20点，好的，这样就找到了可以参考的对话。
![](http://imgsrc.baidu.com/forum/w%3D580/sign=297be1a80d7b02080cc93fe952d8f25f/f882be2397dda144ea49a5bfb5b7d0a20df486d5.jpg)
3.然后我们该怎么在conversation中找到相应的代码段呢？这里因为“CHEAT:_Like_me.”这句话没有汉化，所以直接在conversation里找（这里要注意：conversation文件里每个英文单词之间用“下划线_”连接，搜索时需要记得加上）。
找到了这部分的代码：

dlga_lord_suggest_action:lord_pretalk69631 700 0 {!}CHEAT:_Like_me. 210 1 【1 3 936748722493063440 144115188075855892 20】 NO_VOICEOVER

其实，实现“与对话者提升关系”这一功能的代码只有方括号里的那一部分，这个需要我们自己提炼（用上面提到的软件或者魔球）。总之这里可以先记下这段代码。

4.接下来要考虑我们要把与女士对话的哪句话改为“提升关系”这个功能，如图
![](http://imgsrc.baidu.com/forum/w%3D580/sign=e6e15153dac451daf6f60ce386fd52a5/9a5332950a7b020886c0f6ec65d9f2d3572cc80d.jpg)
进入游戏，这是正常情况下与女士的对话选项，可以发现其中“我是否有这个荣幸，能够对你有进一步的了解呢？尊敬的女士”这句话是最没用的，可以就从这里下刀。

然后我们需要在conversation里找到对应的对话，但是图片中的对话是汉化的，而conversation中只有英文，所以我们要先到汉化文件中找寻：到language/cns文件夹中，找到dialogs.csv文件，这个就是与游戏中对话相关的汉化文件。用记事本打开它，搜索“我是否有这个荣幸”（汉化文件中每个汉字之间以空格符隔开），即可找到：

dlga_lady_talk:lady_relations|我是否有这个荣幸，能够对你有进一步的了解呢 ?尊敬的女士。

而“dlga_lady_talk:lady_relations”这个即是我们接下来需要的，因为可以通过它在conversation文件中搜索；


5.那么，至此，子弹和靶子都已备好，只需要最后一步：命中靶心！
打开conversation.txt，搜索“dlga_lady_talk:lady_relations”
看到：

dlga_lady_talk:lady_relations 69631 883 0May_I_have_the_honor_of_knowing_more_about_you,_my_lady? 【244 0】 NO_VOICEOVER

我们需要修改的即是这一部分，在方括号中的部分替换为“【6 1】 1 3 936748722493063440144115188075855892 20”就行了（第3步得到的代码）。其中【6 1】是我添加的控制字。

到这里，我们就已经达成了最初的目的：修改“对话提升与女士的关系”。
# 修改中常用的txt文件及其简单说明 #
**1.conversation.txt**

----------

正好上面的例子有些地方讲得不是很透，这里再首先提一下。
截取前面一小段（#后面跟着的是我自己添加的注释）：

dialogsfile version 2 #这一行只是声明，没有实际作用
3874 #大控制字，决定着游戏读取的对话数量

dlga_spouse_talk:spouse_household_relation 【69631】 【564 0】 {!}like_me. 【6 1】 1 3936748722493063440 14411518807585589220 NO_VOICEOVER #这就是一个完整的对话

以dlga_xxxx为开始，到NO_VOICEOVER结束，是一个完整的对话段；
可以看到我把很多地方加了方括号，这些部分的代码都是没有实际功能的，属于控制字一类。

像“dlga_spouse_talk:spouse_household_relation”这一类的则是英文字符串，为了标记这个对话，作用是为了方便汉化；而且，无论是魔球还是mbcodeeditor，都不能翻译控制字和字符串（没错就是这么麻烦）！
a．这里69631可以当作一个标识符，它可以表示这个对话的双方是谁，例如此句是玩家与领主的对话，此刻是玩家在说话，也就说明了所有玩家与领主的对话中，标识符都是69631。需要注意的是，如果是领主在与玩家说话，标识符则不同，需要另外寻找。

b．【564 0】和【6 1】也是一种标识符，【564 0】标识的是当前整个对话框，相当于这个对话的ID；【6 1】则是标识的是下一个对话，相当于告诉程序：点完这个对话，接下来出现的对话是哪一句。我们就称564 0为当前标识符、6 1为跳转标识符吧。

其实可以形象理解一下标识符，公交车上，电子音播放，这一站是564 0，下一站是6 1.


为了方便理解，我再从对话中找个例子

dlga_view_char:choose 66316 【23 1】 150 1288230376151711848 Test. 【3059 0】 NO_VOICEOVER

dlga_view_char:choose0 4095 【3059 0】 choose0. 【3060 0】 NO_VOICEOVER

dlga_view_char:choose1 66316 【3060 0】 choose1. 6 3 1541 3 1224979098644774912360287970189639680 0 1530 3 360287970189639680 1224979098644774912 17 1543 3360287970189639680 0 -1 NO_VOICEOVER

dlga_view_char:choose2 66316 【3060 0】 choose2. 6 3 1541 3 1224979098644774912360287970189639680 1 1530 3 360287970189639680 1224979098644774912 42 1543 3360287970189639680 1 -1 NO_VOICEOVER

dlga_view_char:choose3 66316 【3060 0】 choose3. 6 3 1541 3 1224979098644774912 3602879701896396802 1530 3 360287970189639680 1224979098644774912 18 1543 3 360287970189639680 2-1 NO_VOICEOVER

这里有5个对话，让我们分析一下：

第一句dlga_view_char:choose66316 【23 1】 150 1 288230376151711848 Test. 【3059 0】 NO_VOICEOVER

此句的当前标识符为23 1，跳转标识符为3059 0，也就是告诉我们游戏中点完这个对话选项将要跳转到3059 0为当前标识符的对话；
恰好下一句dlga_view_char:choose04095 【3059 0】 choose0. 【3060 0】 NO_VOICEOVER，没错，就是跳转到这里了；

同理再下一句是

dlga_view_char:choose1 66316 【3060 0】 choose1. 6 3 1541 3 1224979098644774912360287970189639680 0 1530 3 360287970189639680 1224979098644774912 17 1543 3360287970189639680 0 -1 NO_VOICEOVER

dlga_view_char:choose2 66316 【3060 0】 choose2. 6 3 1541 3 1224979098644774912360287970189639680 1 1530 3 360287970189639680 1224979098644774912 42 1543 3360287970189639680 1 -1 NO_VOICEOVER

dlga_view_char:choose3 66316 【3060 0】 choose3. 6 3 1541 3 1224979098644774912 3602879701896396802 1530 3 360287970189639680 1224979098644774912 18 1543 3 360287970189639680 2-1 NO_VOICEOVER

奇怪的是，这次有三句对话的当前标识符为3060 0，这不奇怪，因为游戏中的对话多为并列的多个选项，当前标识符相同，也就说明它们是并列的几个选项。

那么通过分析，游戏中对话出现的实际顺序就应该是：

1、dlga_view_char:choose66316 【23 1】 150 1 288230376151711848 Test. 【3059 0】 NO_VOICEOVER
2、dlga_view_char:choose04095 【3059 0】 choose0. 【3060 0】 NO_VOICEOVER

3、dlga_view_char:choose166316 【3060 0】 choose1. 【6 3】 1541 31224979098644774912 360287970189639680 0 1530 3 3602879701896396801224979098644774912 17 1543 3 360287970189639680 0 -1 NO_VOICEOVER

3、dlga_view_char:choose266316 【3060 0】 choose2. 【6 3】 1541 31224979098644774912 360287970189639680 1 1530 3 3602879701896396801224979098644774912 42 1543 3 360287970189639680 1 -1 NO_VOICEOVER

3、dlga_view_char:choose366316 【3060 0】 choose3. 【6 3】 1541 31224979098644774912 360287970189639680 2 1530 3 3602879701896396801224979098644774912 18 1543 3 360287970189639680 2 -1 NO_VOICEOVER

讲到这里，对话这还有个疑问。为什么标识符里第二个数字有时是0，有时是1，甚至更大值呢。其实这里标识符的第二个数字就是前面提到的小控制字，它代表的是本代码段中包含的py行数，默认是0。像【6 3】就说明后面跟着3个py语句。

顺便一提如果跳转符是【6 x】，代表整个对话结束，对话框直接关闭。如果游戏中有些对话卡住了，就是跳转标识符没写对，你可以把它改为【6 x】，怎么样，是不是不知不觉又解决了一个问题？

 **1-1.对话的汉化方法：**


例如，我在conversation.txt文件里添加了一个新对话，
dlga_xxx (省略中间的代码) I’m_your_friend（省略中间的代码）NO_VOICEOVER

那么我需要到cns文件夹里找到dialogs.csv文件，用记事本打开，在任意行添加：

dlga_xxx|我是你的朋友

即可。汉字需要空格符隔开。如果没汉化，游戏内此对话就显示I’m your friend。

**2.menus.txt**

----------
menus，顾名思义，也就是游戏里的菜单，例如城堡的各个选项：进入城堡、管理驻军等等，这些都是写在menu里的，当然具体每个功能的逻辑还是藏在scripts.txt（脚本）里。

a．首先，看到文件的前两行：

menusfile version 1

274

这里的274就是大控制字，代表着这个文件里一共有着274个以menu_xxx为开头的代码段。

b．然后开始分析具体的代码段

这个文件里，每个代码段以menu_xxx开头，“ .”（空格符+实心句号）为结尾。下面给出一个例子：
<pre>
menu_start_game_0
 18374686479671624192Welcome,_adventurer,_to_Mount_and_Blade:_Warband,_Prophesy_of_Pendor._Before_beginning_the_game_you_must_create_your_character._While_in_a_traditional_medieval_society_war_and_politics_are_usually_dominated_by_male_members_of_the_nobility,_this_is_emphatically_not_the_case_in_Pendor_-_men_and_women_are_equals_in_battle,_at_conquest_and_either_gender_may_become_king_or_queen._That_does_not_however_mean_that_you_should_not_choose_to_play_a_character_who_is_not_of_noble_birth._Nobles_may_have_a_somewhat_easier_start,_but_commoners_can_attain_all_of_the_same_goals_--_and_in_fact_may_have_a_much_more_interesting_if_more_challenging_early_game.【none 9】 2133 2 144115188075856132 0 2133 2 144115188075856133 0 2133 2144115188075856134 0 2133 2 144115188075856135 0 2133 2 144115188075856136 02133 2 144115188075856137 0 2133 2 144115188075856138 0 2133 2144115188075856139 0 2133 2 144115188075856140 0 【2】
mno_continue 0 Continue... 1 2060 1 864691128455135239 . mno_go_back 0 Go_back 1 2055 0 .
</pre>

如上述，就是一个完整的menu代码段，其中menu_start_game_0是这个菜单的名字，用处待会儿提到，这个可以自己随便取名，只要格式是“menu_xxx”就行。

【none 9】，这里的9是中型控制字（因为还有更底层的控制字，为了方便就叫这个中型了），代表着后面跟着9行py语句。这个控制字的位置是固定在none后面的。

【2】也是一个中型控制字，代表着当前menu代码段里有2个子菜单，也就是包含两个mno_xxx（具体有几个自己数就行）,常见的功能如进入城堡、管理驻军都是子菜单级别的。这个控制字的位置稍微容易找错些，它就在在第一个mno_xxx的前面。

然后中间那一大段英文（这里这段就是开新档显示的第一段话）则是进入游戏后，那个菜单里会显示的文字，如果未汉化就会显示那些英文，注意，如果是英文，需要在每个单词之间加一个下划线（_）。

c.接着看刚才的例子，上面提到了子菜单的概念，也就是以mno_xxx为开始的小代码段，它从属于menu，却也有着自己的控制字，就叫它小控制字吧。
mno_continue 【0】 Continue... 【1】 2060 1 864691128455135239 【 .】

这里面有两个小控制字，都代表英文字符串后面紧跟的py行数，这个相信能坚持看到这的朋友应该已经有了自己的理解。注意句末还有个实心句号，一定不能忽略，少了会无法进入游戏。

那么再举两个例子，看看读者能不能找对每个控制字的所在。

例1
<pre>
menu_start_game_3 512 Choose_your_scenario: none 【2】 2133 2144115188075856145 0 2133 2 144115188075856145 144115188075856145 【1】
mno_go_back 【0】 Go_back 【1】 2055 0 .
</pre>

例2
<pre>
menu_custom_battle_scene 18374686479671624192 (NO_TRANS)none 【0】【6】
mno_quick_battle_scene_1 0 {!}quick_battle_scene_1 【3】 1911 1792633534417207320 1910 1 720575940379279804 2048 0 . mno_quick_battle_scene_2 0 {!}quick_battle_scene_2 【3】 1911 1792633534417207320 1910 1 720575940379279805 2048 0 . mno_quick_battle_scene_3 0 {!}quick_battle_scene_3 【3】 1911 1792633534417207320 1910 1 720575940379279806 2048 0 . mno_quick_battle_scene_4 0 {!}quick_battle_scene_4 【3】 1911 1792633534417207320 1910 1 720575940379279807 2048 0 . mno_quick_battle_scene_5 0 {!}quick_battle_scene_5 【3】 1911 1792633534417207320 1910 1 720575940379279808 2048 0 . mno_go_back 0 {!}Go_back 【1】 2055 0 .
</pre>
如果都准确无误都找到了，说明menu的控制字规律已经掌握。

**2-1.menu的汉化：**

例如，你增加了这些：
<pre>
menu_xxx none 0 3 mno_1（省略代码） . mno_2（省略代码） . mno_3（省略代码） .
</pre>

找到cns文件夹里的game_menus.csv文件，在任一行添加
<pre>
menu_xxx|菜 单
mno_1|子 菜 单 一
mno_2|子 菜 单 二
mno_3|子 菜 单 三
</pre>
按照这种格式汉化即可。

**3.scripts.txt**

----------

这个文件就是脚本文件，也可以理解为一个函数库。几乎所有的底层功能都写在这里，在需要的时候进行调用。

文件的格式比较好辨认，文件第二行的那个数字就是大控制字，代表一共有多少个脚本。就随便举个例子。
<pre>
set_faction -1
【2】 2133 2 1224979098644774912 20 1 3 936748722493063442432345564227567643 1224979098644774912
</pre>
类似这样的就是一个完整的脚本。都是以xxx_xxx -1命名，直到下一个为止是一个脚本。这里的【2】是小控制字，代表后面跟着2个py语句。每个脚本就一个控制字，多方便。

我们需要注意的一点是，并不是写在这个文件里的脚本在游戏里都会被执行。我们可以把scripts文件理解为函数库，只有在需要的时候调用，不需要的时候闲置就行。调用的py语句的格式是(call_script, "script_xxx", "变量1","变量2",……”变量x”),其中这些变量就相当于函数的实参（学过编程的可以明白，没学过编程却又感兴趣的请自行百度此概念，我没有信心讲好）。

但scripts文件中有一个脚本是特殊的，就是game_start -1，这个是每个mod都会有的，游戏开新档时就会调用的（比如说开局给某个领主随机某个封地就在这里）。

举个例子，我在scripts文件里写了一个新脚本，名为test_1，要想在开局时生效就要在game_start这个脚本里添加一个调用：(call_script, "script_test_1","变量1", "变量2",……”变量x”),然后就可以了。当然脚本的调用不止在这里，触发器、对话、场景等等都会经常用到。

**3-1.形参与实参**
想了一下，还是按我自己的理解解释一下形参（形式参数）和实参（实际参数）。

没记错的话，初中就应该学到数学的方程了。我就拿这个举例，f（x）=x+y，这里的f（x）就是代表一个函数，或者说是一个公式。等号右边的x和y就相当于形参，形参不代表具体某个值，只是一个形式而已。而实参，就是实际传递的值，比如x=1,y=2就是一组实参。

那回到我们讲到的脚本调用，首先我们写好的脚本里一定会有一个或几个形参，然后调用的时候就需要传递相应个数的实参，这样才能成功执行我们想得到的功能。

以下给出一个完整的例子（#后跟着的文字是注释，为了方便理解，就用py展示了）：
<pre>
("check_agent_can_use_remote_weapon_store_item_to_reg5", #脚本名
[
(store_script_param, ":var_0", 1), #给第一个参数赋值为1，并储存（定义第一个形参）
(store_script_param, ":var_1", 2), #给第二个参数赋值为2，并储存（定义第二个形参）
]),
(call_script, "script_ check_agent_can_use_remote_weapon_store_item_to_reg5",":var_1", ":var_2"),
#调用上面那个脚本
</pre>

调用后，":var_1"对应脚本里的第一句（第一个形参），被赋值为1；":var_2"对应脚本里的第二句第二句（第二个形参），被赋值为2。这样调用就结束了。其实这和以前学过的数学方程是一个道理，只是以编程语言的形式展现出来。

**4.troops.txt**

----------

这个文件就是用来记录兵种属性的了。说来惭愧，我对于这个文件的解析度还真不够高，能从txt中得到的信息远不如魔球多。这次讲的是在脱离魔球的帮助下如何修改。

不过这个文件的默认排版看着很舒服，可以很轻松分辨出两个兵种代码之间的间隙。

还是举个例子吧（就拿东方4.0举例了）：

我们要修改天子的属性，首先到cns文件夹里的troops.csv里找，按Ctrl+f搜索“比那 名居 天 子”，注意中文一定是每个字符之间都有一个空格符。

搜索到trp_npc11|比那 名 居 天 子，可以得知trp_npc11就是天子的ID。

接下来我们就可以到troops.txt里搜索“trp_npc11”，看到:
<pre>
trp_npc11 Tenko Tenko 0 334495763 0 0 1 0 0
1.【616 0 617 0691 0 692 0 1177 0 1178 0 1179 0 -1 0 -1 0 -1 0 -1 0 -1 0 -1 0 -1 0 -1 0 -1 0-1 0 -1 0 -1 0 -1 0 -1 0 -1 0 -1 0 -1 0 -1 0 -1 0 -1 0 -1 0 -1 0 -1 0 -1 0 -1 0-1 0 -1 0 -1 0 -1 0 -1 0 -1 0 -1 0 -1 0 -1 0 -1 0 -1 0 -1 0 -1 0 -1 0 -1 0 -1 0-1 0 -1 0 -1 0 -1 0 -1 0 -1 0 -1 0 -1 0 -1 0 -1 0 -1 0 -1 0 -1 0 -1 0 -1 0 -1 0】
2.【13 12 8 9 11】
3.【70 109 122126 50 130 100】
4.【0 0 805306480 17732 348784 0】
579655434276419223908692367186 2008222 0 0 0 0 0
</pre>
我用方括号包住了4块。

1. 物品栏，“616 0 617 0 691 0 692 0 1177 0 1178 0 1179 0 -1 0 -1 0”仔细看看会发现这里面的非0数字就是物品的编号，-1代表空栏。至于物品的编号怎么获得，你需要打开item_kinds.txt，一个一个数，是按顺序往下编的（当然还是魔球直接查看序列号方便了）；

2. 属性栏，一共五个数字，按从左到右顺序分别是力量、敏捷、智力、魅力、等级；

3. 武器熟练度，也是从左到右顺序，分别是单手、双手、长杆、弓箭、弩、投掷、火器；

4. 技能，我会说这个我不会看吗汗死，以后研究出规律再说吧。。。为表歉意，我给出所有技能全为10时这一部分的代码：2684357290 2863311530 2852126890 43690 699040 0
至于性别、阵营、面容什么的，改txt非常艰难，可以说无法翻译，所以说只能魔球或者py。
