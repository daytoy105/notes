#SVG
	· SVG形状 
		1 line
			x1 y1 : 起点坐标 
			x2 y2 : 终点坐标 
			stroke : 画笔颜色
			stroke-width: 画笔大小
			stroke-dasharray ： 点线 或 虚线  （偶数个）
			stroke-opacity: 不透明度
			/*** svg 形状都有 *****/
		2 rect
			x  y  width  height 
			fill ： 填充颜色 
			rx ry : 圆角
		3 circle
			cx  cy ：圆点位置 
			r ：     半径 
		4 ellipse
		 （circle属性）
			rx ry : x y 方向的半径
		5 polygon
			points
		6 polyline
			* 与polygon的不同， 线条不封闭
			* fill 设置为 none
			* line 或 polyline时
				stroke-linecap 指定线的头尾形状
					butt
					round
					square
				stroke-linejoin 线条棱角交叉的效果
					miter ：尖
					round : 圆
					bevel ：平
		6 path
				M ：moveto
				L : lineto
				H : horizontal lineto
				V ：vertical lineto
				C : curveto  （三次贝塞尔曲线）
				S : smooth curveto 
				Q : quadratic Belzier curve  （二次贝塞尔曲线）
				T : smooth quadratic Belzier curveto
				A : elliptical Arc
				Z : closepath
			* 每个路径都必须以moveto命令开始， x和y坐标，用空格或逗号分隔 
			* 小写命令的坐标是相对的，大写命令的坐标是绝对的
			* 圆弧A 后接7个参数
				点所在的x y 半径 （1 2）
				椭圆x轴转换角度 （3）
				圆弧角度，小于180度，为0；大于等于180度，为1 （4）
				圆弧以负角度绘制为0， 以正角绘制为 1   （5）
				终点x y 坐标 （6 7 ）
			* 多边二次贝塞尔曲线
				<path d="M30 100 Q 80 30, 100 100, 130 65, 200 80"/>
				===> 绘制一条从(30,100)到(100,100)并且控制点在(80,30)的曲线，然后绘制一条到(200,80)且控制点在(130,65)的曲线
			* 三次贝塞尔曲线 
				<path d="M30 100 C 50 50, 70 20, 100 100, 110 130, 45 150, 65 100"/>
				===> 绘制一条从(30,100)到(100,100),控制点在(50,50)和(70,20)的三次曲线，然后绘制一条曲线，折回(65,100)，控制点在(110,130)和(45,150)
		7 文本
			* 属性  font  text-decoration stroke stroke-width fill  text-anchor(对齐)
			* 文本可以指定起点，无法预知终点，（不知文本的长度）
			* 标签  <text> 与  <tspan>
			* dy dx : 垂直水平偏移 （手动设置）
			* baseline-shift ： sub (下标)  super(上标)
			* textLength ：设置文本长度
				lengthAdjust
					-- spacing : 默认值，只调整字符间距
					-- spacingAndGlyphs：调整字符间距及字符本身大小
			 * writing-mode
				 -- tb   (纵向文本)
				 glyph-orientation-vertical  (没效果)
					 90 : 默认值， 文本垂直排列，竖向显示
					 0 ：文本垂直排列，横向显示
				* <switch>
					 systemLanguage   语言选择
				* <font-face> 使用自定义字体
				* <textPath/> 文本路径
					 居中 ： text-anchor:middle   startOffset:50%
		** SVG 编辑器
	· 视口
		viewBox : (x y w h)
		preserveAspectRatio : 指定对齐方式
			alignment：
				xMin  xMid  xMax
				yMin  yMid  yMax
			meet 
			slice : 切割
			none : 不保留宽高比
	· 分组和引用元素
		<g> 
			会将其所有子元素作为一个组合，id命名
		<use>
			为定义在<g>元素内的组合或者任意独立图形元素提供类似复制粘贴的能力
			<use>不能覆盖原始图形的颜色
		<defs>
			其<use>元素可以自定义颜色
		<symbol>
			分组，与<g>的差别，<symbol>元素不会显示
			可设置属性 viewBox  preserveAspectRatio 
	· 坐标系统变换
		 笛卡儿坐标系统转换 
			 y轴与SVG的默认约定“上下相反”， x轴相同
		 rotate(angle, centerX, centerY) 
			 围绕中心点旋转
		 translate(-centerX*(factor-1), -centerY*(factor-1)) scale(factor) 
			 围绕中心点缩放
		 skewX  &  skewY
			 skewX(angle): 根据指定的angle倾斜所有x坐标，从视觉上，垂直线出现角度
			 skewY(angle): 根据指定的angle倾斜所有y坐标，从视觉上，水平线出现角度 
		 matrix(a b c d e f ) : 指定一个六个值组成的矩阵变换
	· 图案与渐变
		<pattern>
		<linearGradient>
	· 裁剪和蒙版
		<clipPath>  和 clip-path
	· SVG动画
			* <animate>  
					attributeName ：动画应持续改变的值
					attributeType ：取值 XML  或 css
					from to ：属性的起始和结束值 
					begin end dur ： 动画开始时间 、结束时间、持续时间
					fill ：取值 freeze （值为 to） , 默认 remove (from)
			* 同步动画
				id.end ： 绑定动画的开始时间为另一个动画的开始或者结束 (id 为另一个动画的属性)
				id.begin+1.2s : 另一个动画的开始后添加一个1.2s的偏移量
			* 重复动作
				repeatCount  /   repeatDur   
				 indefinite: 动画持续到用户离开界面
			* <set> 
				 引入不可见的文本项
			* <animateTransform>
				 适用于旋转、平移、缩放或倾斜变换
			* <animateMotion> 
				 复杂路径运动  : path  /  <mpath>







