#owl carousel源码分析
##1.总体功能介绍
整体是对stage中item的css操作来实现animation，transform，transisition的动画效果，item中可存放image或者video。支持用户通过鼠标操作或者touch操作。

##2.初始化过程
在页面中调用owlCarousel开始整个插件的初始化过程。
		
		```
		this.each(function() {...}
		```
针对每个选中element添加属性：

-- data属性保存owl实例

-- 设置'next', 'prev', 'to', 'destroy', 'refresh', 'replace', 'add', 'remove'这些事件的_default方法，如果namespace中包含owl则不会调用被选节点上与事件同名的方法。jquery的trigger方法中
	
	···
	
	elem[ type ]();
	
	···
	
	其中elem为选中节点，type为方法名称。type中不包含namespace信息。
-- 被选择element绑定'next', 'prev', 'to', 'destroy', 'refresh', 'replace', 'add', 'remove'事件处理。这些事件触发时e.relatedTarget是owl实例时，不会被处理。只能是其他触发方式才会被处理。

###1.owl
owl实例初始化，具体包括：

--将用户定义的css属性与缺省的css属性整合保存到this.options.将选中的element保存到this.$element.

--设置onResize，onThrottledResize的handler，保存在owl实例的_handlers数组属性中。初始化所有plugin，保存在owl实例的_plugins数组属性，在_pipe数组中保存worker信息。

--调用setup，依据viewport和owlcarousel的入参responsive组合设定窗口大小，更新setting信息，并更新被选中节点的的class部分内容为owl-responsive[-n]，其中n为responsive中的，更新_breakpoint值,_invalidate[settings]=true。触发change和changed事件。相应的plugin会处理已注册事件。

-调用initialize，记录当前状态this._states.current[name]，触发initialize事件。现有代码没有处理initialize事件。依据条件设置rtl样式。对于autowidth，可提前装载图片内容。添加stage及子元素，调用refresh，完成setup调用，update调用，update调用中完成item和setting初始化过程works的顺序执行，设置被选元素宽度：this._width, 设置cache.current为当前被选中的item，删除.cloned的节点，设置所有item的css信息，并换存在cache.css中，设置每个item的width到this._widths. 为保证无缝切换，增加cloned节点item。设置每个item的横向坐标到this._coordinates，设置items上级元素stage的总宽度以及左右padding信息。对grid布局设置item的css信息。设置当前节点动画信息，设置正在展示的item为active，设置被选item居中。在stage注册拖拽事件，resize事件以及变换结束事件。

-owl初始化过程中，会触发change，changed, initialize, initialized，translate, translated, refresh, refreshed事件，插件组件会处理这些事件.
###2.AutoRefresh
设置this._core为owl实例，整合css信息，注册initialized事件。事件处理方法是watch方法，watch设定刷新间隔调用refresh。可能调用owl的refresh。
###3.Lazy
设置this._core为owl实例，整合css信息，注册initialized.owl.carousel change.owl.carousel resized.owl.carousel三个事件。
###4.AutoHeight
设置事件和处理函数，事件包括：initialized, changed,loaded. 这两个事件load, resize是直接设置在window上。这些事件的处理都会调用其中的update方法。update方法设置stage上层节点的高度为所有可见item高度的最大值。
###5.Video
绑定initialized, resize, refreshed, changed, prepared事件处理，.owl-video-play-icon节点的click事件，用户点击icon后，在iframe中添加src属性，浏览器完成下载显示。相应的fetch,thumbnail,stop基本用不上。
###6.animate
绑定change，drag，dragged，translated，translate事件处理，animate插件负责对应item的class设置。与animate配合的是在owl中animate方法会在总的被选节点设置transform和transition相关参数。
###7.autoplay
绑定changed，initialized，play，stop，mouseover，mouseleave，touchstart，touchend事件。调用play，stop，pause方法，实际上对item的位置和定时刷新时间的设置。
###8.navigation
绑定prepared，added，remove，changed，initialized，refreshed事件，调用update，draw等方法实际是对dot进行定位操作。
###9.hash
绑定initialized，prepared，changed，hashchanged事件，实际操作item位置信息。
###10.support
对浏览器是否支持transition，animation，transform进行测试。并记录在support上。
##3.插件配置
插件参数的配置可参考每个参数的意义进行设置。
##4.插件开发方法
owl的开发方式主要是采用IIFE模式，同时使用原型链将主题方法和插件方法关联起来。主体OWL的方法主要对stage和item进行操作，插件对每个item进行具体操作。整个操作会设置state标志，防止重复操作。主体owl和每个插件都有destroy方法，防止内存泄漏。
