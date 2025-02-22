.. index:: ! psxy

psxy
====

:官方文档: :doc:`gmt:psxy`
:简介: 在图上绘制线段、多边形和符号

该命令既可以用于画线段（多边形可以认为是闭合的线段）也可以用于画符号，唯一的
区别在于是否使用了 ``-S`` 选项。在不使用 ``-S`` 选项的情况下，默认会将所有的
数据点连成线，使用 ``-S`` 选项则仅在数据点所在位置绘制符号。

可选选项
--------

``-A[m|p|x|y]``
    修改两点间的连接方式。

    地理投影下，两点之间默认沿着大圆弧连接。

    #. ``-A`` ：忽略当前的投影方式，直接用直线连接两点
    #. ``-Am`` ：先沿着经线画，再沿着纬线画
    #. ``-Ap`` ：先沿着纬线画，再沿着经线画

    笛卡尔坐标下，两点之间默认用直线连接。

    #. ``-Ax`` 先沿着X轴画，再沿着Y轴画
    #. ``-Ay`` 先沿着Y轴画，再沿着X轴画

    下图中，黑色曲线为默认情况；红线为使用 ``-A`` 的效果；蓝线为使用 ``-Ap``
    的效果；黄线为使用 ``-Am`` 的效果：

    .. figure:: /images/psxy_-A.*
       :width: 100%
       :align: center

       psxy -A选项示意图

    注：由于这里投影比较特别，所以沿着经线的线和沿着纬线的线，看上去都是直线，
    在其他投影方式下可能不会是这样。

``-C<cpt>``
    指定CPT文件或颜色列表

    该选项后跟一个CPT文件名，也可以使用 ``-C<color1>,<color2>,...`` 语法在
    命令行上临时构建一个颜色列表，其中 ``<color1>`` 对应Z值为0的颜色， ``<color2>``
    对应Z值为1的颜色，依次类推。

    #. 若绘制符号（即使用 ``-S`` 选项），则符号的填充色由数据的第三列Z值决定，
       其他数据列依次后移一列
    #. 若绘制线段或多边形（即未使用 ``-S`` 选项），则需要在多段数据的头段中指定
       ``-Z<val>`` ，然后从cpt文件中查找 ``<val>`` 所对应的颜色，以控制线段或
       多边形的线条颜色

    下面的例子展示了 ``-C<color1>,<color2>..`` 用法::

        gmt psxy -JX10c/10c -R0/10/0/10 -B1 -Cblue,red -W2p > test.ps << EOF
        > -Z0
        1 1
        2 2
        > -Z1
        3 3
        4 4
        EOF

``-D<dx>/<dy>``
    设置符号的偏移量。

    该选项会将要绘制的符号或线段在给定坐标的基础上偏移 ``<dx>/<dy>`` 距离。
    若未指定 ``<dy>`` ，则默认 ``dy=dx`` 。

``-E[x|y|X|Y][+a][+cl|f][+n][+w<cap>][+p<pen>]``
    绘制误差棒。

    默认会绘制X和Y两个方向的误差棒。 ``x|y`` 表示只绘制X方向和/或Y方向的误差棒，
    此时输入数据的格式为（具体格式由选项决定）::

        X  Y   [size]     [X_error]    [Y_error]  [others]

    例如，X方向误差为1::

        echo 5 5 1 | gmt psxy -R0/10/0/10 -JX10c/10c -B1 -Sc0.1c -Ex -W2p > test.ps

    X方向误差为1，Y方向误差为0.5::

        echo 5 5 1 0.5 | gmt psxy -R0/10/0/10 -JX10c/10c -B1 -Sc0.1c -Exy -W2p > test.ps

    使用 ``+a`` 表示X方向和/或Y方向为非对称误差棒，此时输入数据的格式为::

        X  Y   [size]   [X_left_error X_right_error]  [Y_left_error Y_right_error] [others]

    例如::

        echo 5 5 1 0.4 0.5 0.25 | gmt psxy -R0/10/0/10 -JX10c/10c -B1 -Sc0.1c -Exy+a -W2p > test.ps

    使用 ``X`` 和 ``Y`` 则绘制box-and-whisker（即stem-and-leaf）符号。以 ``-EX``
    为例，此时数据数据格式为::

        X中位数  Y  0%位数 25%位数 75%位数 100%位数

    25%到75%之间的方框内可以用 ``-G`` 选项填充颜色::

        echo 5 5 4 4.25 5.4 7 | gmt psxy -R0/10/0/10 -JX10c/10c -B1 -Sc0.1c -EX -Gred -W2p > test.ps

    - 若使用 ``-EXY`` ，则输入数据中至少需要10列；
    - 若在X或Y后加上了 ``+n`` ，则需要在额外的第5列数据指定中位数的不确定性。
    - ``+w<cap>`` 控制误差棒顶端帽子的长度，默认值为7p
    - ``+p<pen>`` 控制误差棒的画笔属性，默认值为 ``defalut,black,solid``
    - 在使用 ``-C`` 选项时，可以从CPT文件中查找到符号所对应的颜色

      - ``+c`` 表明将颜色应用于符号填充色和误差棒画笔属性
      - ``+cf`` 表明仅将颜色用于填充符号
      - ``+cl`` 表面仅将颜色用于设置误差棒画笔属性，并关闭符号填充色

``-F[c|n|r][a|f|s|r|<refpoint>]``
    修改数据点的分组和连接方式。

    数据的分组方式有三种：

    #. ``a`` 忽略所有数据段头记录，即将所有文件内的所有数据点作为一个单独的组，
       并将第一个文件的第一个数据点作为该组的参考点
    #. ``f`` 将每个文件内的所有点分在一个组，并将每一组内的第一个点作为该组的参考点
    #. ``s`` 每段数据内的点作为一组，并将每段数据的第一个点作为该组的参考点
    #. ``r`` 每段数据内的点作为一组，并将每段数据的第一个点作为该组的参考点，
       每次连线后将前一个点作为新的参考点，该选项仅与 ``-Fr`` 连用（似乎与
       ``-Fcs`` 等效？）
    #. ``<refpoint>`` 指定某个点为所有组共同的参考点

    在确定分组后，还可以额外定义组内各点的连接方式：

    - ``c`` 将组内的点连接成连续的线段
    - ``r`` 将组内的所有点与组内的参考点连线
    - ``n`` 将每个组内的所有点互相连线

    在不使用 ``-F`` 选项的情况下，默认值为 ``-Fcs`` 。该选项的具体示例在后面给出。

``-G<fill>``
    设置符号或多边形的填充色。多段数据中数据段头记录中的 ``-G`` 选项会覆盖命令行中的设置。

``-I<intens>``
    模拟光照效果

    ``<intens>`` 的取值范围为-1到1，用于对填充色做微调以模拟光照效果。正值
    表示亮色，负值表示暗色，零表示原色。

``-L[+b|d|D][+xl|r|<x0>][+yl|r|<y0>][+p<pen>]``
    构建闭合多边形。

    默认情况下，psxy只将数据点连起来，若首尾两个点不相同，则不会形成闭合多边形。
    使用 ``-F`` ，则自动将数据的首尾两个点连起来，形成闭合多边形。

    除了简单的首尾相连之外，还可以给线段加上包络线（类似于线段的误差）：

    #. ``+d`` build symmetrical envelope around y(x) using deviations dy(x) given in extra column 3
    #. ``+D`` build asymmetrical envelope around y(x) using deviations dy1(x) and dy2(x) from extra columns 3-4.
    #. ``+b`` build asymmetrical envelope around y(x) using bounds yl(x) and yh(x) from extra columns 3-4.

    #. ``+xl|r|<x0>`` connect first and last point to anchor points at either xmin, xmax, or x0
    #. ``+yb|t|<y0>`` connect first and last point to anchor points at either ymin, ymax, or y0.

    Polygon may be painted (**-G**) and optionally outlined by adding **+p**\ *pen* [no outline].

``-N[r|c]``
    区域范围外的符号不会被裁剪，而会被正常绘制。

    默认情况下，位于 ``-R`` 范围外的符号不会被绘制的。使用该选项使得即便符号的
    坐标位于 ``-R`` 指定的范围外，也会被绘制。需要注意的是，该选项对线段或多边
    形无效，线段和多边形总会被区域的范围裁剪。

    对于存在周期性的地图而言，若符号出现在重复边界上，则会被重复绘制两次。比如::

        gmt psxy -R0/360/-60/60 -JM10c -Bx60 -By15 -Sc2c > test.ps << EOF
        360 0
        EOF

    会在地图的左右边界处分别两个半圆，该行为可以通过 ``-N`` 选项修改：

    #. ``-N`` 关闭裁剪，符号仅绘制一次
    #. ``-Nr`` 关闭裁剪，但符号依然绘制两次
    #. ``-Nc`` 不关闭裁剪，但符号仅绘制一次

``-T``
    忽略所有输入文件，包括标准输入流

    该选项会忽略命令行中的输入文件以及标准输入流，在Linux下相当于将空文件
    ``/dev/null`` 作为输入文件，因而该命令不会在PS文件中绘制任何图形。

    该选项有如下几个用途：

    #. ``gmt psxy -J$J -R$R -T -K > $PS`` 只写入文件头，见 ``-K`` 和 ``-O`` 选项的介绍
    #. ``gmt psxy -J$J -R$R -T -O >> $PS`` 只写入文件尾，见 ``-K`` 和 ``-O`` 选项的介绍
    #. ``gmt psxy -J$J -R$R -T -X10c -Y10c >> $PS`` 只移动坐标原点而不绘制任何图形

``-W[<pen>][<attr>]``
    设置线段或符号轮廓的画笔属性。

    #. ``<pen>`` 见 :doc:`/basis/pen` 一节
    #. 若使用了 ``+cl`` 则表示线条颜色由CPT文件控制
    #. 若使用了 ``+cf`` 则符号的填充色由CPT文件控制
    #. 若使用了 ``+c`` 则表示线条颜色和符号填充色同时由CPT文件控制
    #. ``-W`` 选项后还可以加上额外的选项，可以指定线条的额外属性，见 :doc:`/basis/line` 一节

``-S`` 选项
-----------

使用 ``-S`` 选项，则表示要绘制符号。 ``-S`` 选项的基本语法是::

    -S[<symbol>][<size>[<u>]]

其中 ``<symbol>`` 指定了符号类型， ``<size>`` 为符号的大小， ``<u>`` 为 ``<size>`` 的单位。

不同的符号类型，需要的输入数据格式也不同，但可以统一写成（用 ``...`` 代表某符号
特有的输入列）::

    X   Y   ...

``-S-|+|a|c|d|g|h|i|n|s|t|x|y|p``
    绘制一些简单的符号。

    这几个符号比较简单，输入数据中不需要额外的列：

    - ``-S-`` ：短横线， ``<size>`` 是短横线的长度；
    - ``-S+`` ：加号， ``<size>`` 是加号的外接圆的直径；
    - ``-Sa`` ：五角星（st\ **a**\ r）， ``<size>`` 是外接圆直径；
    - ``-Sc`` ：圆（\ **c**\ ircle）， ``<size>`` 为圆的直径；
    - ``-Sd`` ：菱形（\ **d**\ iamond）， ``<size>`` 为外接圆直径；
    - ``-Sg`` ：八边形（octa\ **g**\ on）， ``<size>`` 为外接圆直径；
    - ``-Sh`` ：六边形（**h**\ exagon）， ``<size>`` 为外接圆直径；
    - ``-Si`` ：倒三角（**i**\ nverted triangle）， ``<size>`` 为外接圆直径；
    - ``-Sn`` ：五边形（pe\ **n**\ tagon）， ``<size>`` 为外接圆直径；
    - ``-Sp`` ：点，不需要指定 ``<size>`` ，点的大小始终为一个像素点；
    - ``-Ss`` ：正方形（\ **s**\ quare）， ``<size>`` 为外接圆直径；
    - ``-St`` ：三角形（\ **t**\ riangle）， ``<size>`` 为外接圆直径；
    - ``-Sx`` ：叉号（cross）， ``<size>`` 为外接圆直径；
    - ``-Sy`` ：短竖线， ``<size>`` 为短竖线的长度；

    对于小写符号 ``acdghinst`` ， ``<size>`` 表示外接圆直径；
    对于大写符号 ``ACDGHINST``， ``<size>`` 表示符号的面积与直径为 ``<size>``
    的圆的面积相同。

    下图给出了上面所给出的symbol所对应的符号：

    .. figure:: /images/psxy_symbols.*
       :width: 100%
       :align: center
       :alt: psxy simple symbols

       psxy -S选项示意图

    除了上述简单的符号之外，还有更多复杂的符号。

``-Sb|B[<size>[<u>]][b[<base>]]``
    绘制垂直bar。

    ``-Sb`` 用于在X坐标处绘制一个从 ``<base>`` 到Y位置的垂直bar。

    #. ``<size>`` 是bar宽度，其单位可以是长度单位 ``c|i|p`` ，也可以用 ``u``
       表示X方向单位
    #. 若不指定 ``b<base>`` ，其默认值为ymin
    #. 指定 ``b<base>`` ，为所有数据点指定base值
    #. 加上 ``b`` 但未指定 ``<base>`` ，则需要额外的一列数据来指定base的值
    #. ``-SB`` 与 ``-Sb`` 类似，区别在于 ``-SB`` 绘制水平bar

    ::

        gmt psxy -R0/10/0/5 -JX15c/5c -B1 -Sb1cb > test.ps << EOF
        2 3 1 0.5
        4 2 1 1.5
        8 4 1 2.5
        EOF

``-Se|E``
    绘制椭圆

    ``-Se`` 用于绘制椭圆。对于椭圆而言， ``<size>`` 是不需要的。此时输入数据的格式为::

        X   Y   方向   长轴长度    短轴长度

    其中方向是相对于水平方向逆时针旋转的角度，两个轴的长度都使用长度单位，即 ``c|i|p``

    ``-SE`` 选项与 ``-Se`` 类似，区别在于：

    - 第三列为方位角（相对于正北方向旋转的角度）。该角度会根据所选取的地图投影变换成角度
    - 对于线性投影，长短轴的长度单位为数据单位，即与 ``-R`` 中数据范围的单位相同
    - 对于地理投影，长轴和短轴的长度单位为千米，且不可更改

    用长度单位指定一个椭圆::

        echo 180 0 45 5c 3c | gmt psxy -R0/360/-90/90 -JN15c -B60 -Se > test.ps

    线性投影下 ``-SE`` 的长短轴的单位为数据单位::

        echo 180 0 45 300 100 | gmt psxy -R0/360/-90/90 -JX10c -B60 -SE > test.ps

    地理投影下 ``-SE`` 的长短轴的单位是地理单位，默认长度单位为千米::

        echo 80 0 45 22200 11100 | gmt psxy -R0/360/-90/90 -JN15c -B60 -SE > test.ps
        echo 80 0 45 200d  100d  | gmt psxy -R0/360/-90/90 -JN15c -B60 -SE > test2.ps

    若长短轴长度相等，则椭圆退化成圆，可以用于绘制直径以千米为单位的圆，从而解决了
    ``-Sc`` 只能用长度单位而不能用距离单位画圆的不足。这一特性可以用于绘制等震中
    距线。比如如下命令可以绘制30度等震中距线::

        echo 80 0 0 60d 60d | gmt psxy -R0/360/-90/90 -JN15c -B60 -SE > test.ps

    上面示例的输入数据中，方向和短轴长度都是多余的，所以GMT提供了 ``-SE-[<size>]``
    选项用于绘制直径为 ``<size>`` 的圆，若未指定 ``<size>`` ，则需要在数据中指定
    圆的直径。比如30度和60度等震中距线可以用如下命令绘制::

        gmt psxy -R0/360/-90/90 -JN15c -B60 -SE- > test.ps << EOF
        180 0 60d
        180 0 120d
        EOF

``-Sf<gap>[/<size>][+l|+r][+b+c+f+s+t][+o<offset>][+p[<pen>]]``
    绘制front，即在线段上加上符号以表示断层等front

    #. ``<gap>`` 线段上符号之间的距离，若 ``<gap>`` 为负值，则解释为线段上符号的个数
    #. ``<size>`` 为符号大小

       #. 若省略了 ``<size>`` ，则默认为 ``<gap>`` 的30%
       #. 若 ``<gap>`` 为负值，则 ``<size>`` 是必须的

    #. ``+l`` 和 ``+r`` 分别表示将符号画在线段的左侧还是右侧，默认是绘制在线段中间
    #. ``+b`` 符号为box
    #. ``+c`` 符号为circle
    #. ``+t`` 符号为triangle
    #. ``+f`` 符号表示断层（fault），默认值。
    #. ``+s`` 符号表示断层的滑动（slip），用于表示左旋或右旋断层。其可以接受一个
       可选的参数来控制绘制矢量时的角度。也可以用 ``+S`` 绘制一个弧形箭头
    #. ``+o<offset>`` 将线段上的第一个符号相对于线段的起点偏离 ``<offset>`` 距离，默认值为0
    #. 默认符号的颜色与线段颜色相同（ ``-W`` 选项），可以使用 ``+p<pen>`` 为符号\
       单独指定颜色，也可以使用 ``+p`` ，即不绘制符号的轮廓。

    下面的例子分别绘制了 ``+b`` 、 ``+c`` 、 ``+f`` 、 ``+s`` 、 ``+t`` 所对应的符号：

    .. literalinclude:: /scripts/psxy_-Sf.sh
       :language: bash

    .. figure:: /images/psxy_-Sf.*
       :width: 100%
       :align: center
       :alt: psxy -Sf example

       psxy -Sf示意图

``-Sj|J``
    绘制旋转矩形

    其输入数据为::

        X   Y   方向    X轴长度   Y轴长度

    方向为相对于水平方向逆时针旋转的角度。

    ``-SJ`` 与 ``-Sj`` 类似，区别在于：

    #. 输入的第三列是方位角
    #. 对于地理投影，X轴和Y轴长度的单位为地理单位，默认为 km
    #. 对于线性投影，X轴和Y轴长度的单位与 ``-R`` 选项中数据范围的单位相同

    若矩形的长宽相等，则矩形退化成正方形，此时可以使用 ``-SJ-<size>`` 。
    ``<size>`` 是正方形的长度，若未指定 ``<size>`` 则需要在输入数据的第三列指定长度。

``-Sk<name>/<size>``
    绘制自定义的符号。

    GMT支持自定义符号，该选项会依次在当前目录、 ``~/.gmt`` 、 ``$GMT_SHAREDIR/custom``
    目录中寻找自定义符号的定义文件 ``<name>.def`` 。定义文件中的符号默认其大小为1，
    然后会根据 ``<size>`` 对其进行缩放。关于如何自定义符号，见中文手册。

``-Sl<size>+t<string>+j<justify>``
    绘制文本字符串

    该选项的功能与 :doc:`pstext` 类似，不知道为何要设计这个选项。

    #. ``<size>`` 文本串的大小
    #. ``+t<string>`` 指定文本串
    #. ``+j<justify>`` 修改文本串的对齐方式，默认为 ``CM``

``-Sm|M<size>``
    绘制数学圆弧

    输入数据的格式为::

        X  Y  radius_of_arc  start_direction  stop_direction

    #. ``<size>`` 为矢量箭头的长度
    #. 圆弧的线宽由 ``-W`` 选项设定
    #. ``-SM`` 选项与 ``-Sm`` 完全相同，只是当圆弧的夹角恰好是90度是，
       ``-SM`` 会用直角符号来表示
    #. 圆弧的两端可加上额外的箭头，见 :doc:`/basis/vector` 一节

    .. literalinclude:: /scripts/psxy_-Sm.sh
       :language: bash

    .. figure:: /images/psxy_-Sm.*
       :width: 50%
       :align: center
       :alt: psxy angle arc

       psxy -Sm 示意图

``-Sq[<type>]<info>[:<labelinfo>]``
    绘制quoted lines，即带标注的线段，比如等值线、带断层名的断层线等

    ``<type>`` 有6种可选的方式：

    #. ``d<dist>[<u>]/[<frac>]`` 指定标签之间的距离，单位 ``<u>`` 为 ``c|i|p`` ；
       ``<frac>`` 表示将第一个标签放在距离quoted lines起点 ``<frac>*<dist>`` 处
    #. ``D<dist>[<u>]/[<frac>]`` 指定标签之间的距离，单位 ``<u>`` 可以取 ``e|f|k|M|n|u|d|m|s``
    #. ``f<ffile.d>`` 根据ASCII文件 ``<ffile.d>`` 的内容确定标签的位置。仅当
       ``<ffile.d>`` 中指定的标签位置与quoted lines上数据点的位置完全匹配时才会被绘制
    #. ``l<line1>[,<line2>,...]`` 指定一个或多个以逗号分隔的线段，标签会放在线段
       与quoted line相交的地方。 ``<line>`` 的格式为
       ``start_lon/start_lat/stop_lon/stop_lat`` ，其中 ``start_lon/start_lat``
       以及 ``stop_lon/stop_lat`` 可以用锚点中的两字符替换。
    #. ``L<line1>[,<line2>,...]`` 与 ``l`` 类似，只是将线段解释为两点之间的大圆路径
    #. ``n<n_label>`` 指定等间隔标签的数目，见官方文档
    #. ``N<n_label>`` 见官方文档
    #. ``s<n_label>`` 见官方文档
    #. ``S<n_label>`` 见官方文档
    #. ``x<xfile.d>`` 见官方文档
    #. ``X<xfile.d>`` 见官方文档

    ``<labelinfo>`` 用于控制标签的格式，其可以是下面子选项的任意组合，详情见官方文档：

    #. ``+a<angle>``
    #. ``+c<dx>/<dy>``
    #. ``+d``
    #. ``+e``
    #. ``+f<font>``
    #. ``+g<color>``
    #. ``+j<just>``
    #. ``+l<label>``
    #. ``+L<label>``
    #. ``+n<dx>/<dy>``
    #. ``+o``
    #. ``+p<pen>``
    #. ``+r<min_rad>``
    #. ``+t[<file>]``
    #. ``+u<unit>``
    #. ``+v``
    #. ``+w``
    #. ``+x[<first>,<last>]``

``-Sr``
    绘制矩形

    ``<size>`` 对该符号无效，其输入格式为::

        X   Y   X轴长度   Y轴长度

``-SR``
    绘制圆角矩形

    ``<size>`` 对该符号无用。其输入格式为::

        X   Y   X轴长度     Y轴长度     圆角半径

``-Sv|V|=``
    绘制矢量

    ``-Sv`` 用于绘制矢量，输入数据格式为::

        X   Y   方向    长度

    #. ``<size>`` 为矢量箭头的长度
    #. 矢量宽度由 ``-W`` 控制
    #. 更多箭头的属性见 :doc:`/basis/vector` 一节
    #. ``-SV`` 与 ``-Sv`` 类似，区别在于第三列是方位角而不是方向
    #. ``-S=`` 与 ``-SV`` 类似，区别在于第四列长度的单位是地理单位

    ::

        echo 2 2 45 5c | gmt psxy -R0/10/0/10 -JX10c/10c -B1 -Sv1c+e -W2p > test.ps

``-Sw|W[+a|+r]``
    绘制楔形饼图（pie **w**\ edge），即饼图中的一个切片。加上 ``+a`` 表示只
    绘制弧线， ``+r`` 表示只绘制径向线。

    楔形饼图所需要的输入数据格式为::

        X   Y   start_direction     stop_direcrion

    #. ``<size>`` 是楔形饼图所对应的圆的\ **直径**
    #. 对于 ``-Sw`` ，第三、四列是楔形的开始和结束方向，其中方向定义为相对于
       X轴正方向（即东向）逆时针旋转的角度
    #. 对于 ``-SW`` ，第三、四列是楔形的开始和结束方位角，其中方位角定义为
       相对于北向顺时针旋转的角度。对于地理楔形而言， ``<size>`` 代表径向地理距离而不是


    下面的示例分别用 ``-SW`` 和 ``-Sw`` 画了两个不同大小的楔形饼图：

    .. literalinclude:: /scripts/psxy_-Sw.sh
       :language: bash

    .. figure:: /images/psxy_-Sw.*
       :width: 100%
       :align: center
       :alt: psxy pie wedge

       psxy -Sw示意图。

       左边-Sw，右边-SW；图中1格表示1cm。

``-S~[d|D|f|l|L|n|N|s|S|x|X]<info>[:<symbolinfo>]``
    绘制decorated line，即带有符号的线段。详见官方文档。

输入数据格式
------------

``-S`` 选项相对复杂，与不同的选项连用，或者后面接不同的参数，所需要的输入数据的
格式也不同。不管是什么符号，至少都需要给定符号的位置，即X和Y是必须的::

    X   Y

不同的符号，可能还需要额外的信息，统一写成（用 ``...`` 代表某符号特有的输入列）::

    X   Y   ...

若 ``-S`` 指定了符号类型但未指定大小，即 ``-S<symbol>`` ，若该符号类型需要指定
大小，则需要将符号大小放在输入数据的\ **第三列**\ ，其他输入数据的列号延后，
此时数据格式为::

    X   Y   size    ...

若size<=0，则跳过该记录行。

若 ``-S`` 选项后未指定符号代码，则符号代码必须位于输入文件的\ **最后一列**\ ::

    X   Y   ...   symbol

若使用了 ``-C`` 和 ``-S`` 选项，则符号的填充色由数据的第三列决定，其他字段依次后移::

    X   Y  [Z]   ...  symbol

因而总结一下输入数据的格式为::

    x  y  [Z]  [size]  ...   [symbol]

其中 ``...`` 为某些符号所要求的特殊的数据列， ``symbol`` 是未指定符号时必须的
输入列， ``size`` 是未指定大小时的输入列。

多段数据
--------

对于多段数据而言，每段数据的头段记录中都可以包含一些选项，以使得不同段数据拥有
不同的属性。头段记录中的选项会覆盖命令中选项的参数：

- ``-Gfill`` ：设置当前段数据的填充色
- ``-G-`` ：对当前数据段关闭填充
- ``-G`` ：恢复到默认填充色
- ``-W<pen>`` ：设置当前段数据的画笔属性
- ``-W`` ：恢复到默认画笔属性 ``MAP_DEFAULT_PEN``
- ``-W-`` ：不绘制轮廓
- ``-Z<zval>`` ：从cpt文件中查找Z值<zval>所对应的颜色作为填充色
- ``-ZNaN`` ：从cpt文件中获取NaN颜色

示例
----

最简单的命令，绘制线段或多边形，此时数据输入需要两列，即X和Y::

    gmt psxy -R0/10/0/10 -JX10c -B1 > test.ps << EOF
    3 5
    5 8
    7 4
    EOF

下面的脚本展示了 ``-F`` 选项的用法：

.. literalinclude:: /scripts/psxy_-F.sh
   :language: bash

.. figure:: /images/psxy_-F.*
   :width: 100%
   :align: center

   psxy -F选项示意图

``-L`` 选项的示例：

.. literalinclude:: /scripts/psxy_-L.sh
   :language: bash

.. figure:: /images/psxy_-L.*
   :width: 100%
   :align: center

   psxy -L选项示意图

更多示例见 :doc:`/gallery/lines`\ 。
