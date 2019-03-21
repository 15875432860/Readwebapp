<template>
<div class="ebook">
	<titlebar :ifTitleAndMenuShow="ifTitleAndMenuShow"></titlebar>
	<!--点击翻页隐藏显示区域 -->
	<div class="read-wrapper">
		<div id="read"></div>
		<div class="mask">
			<div class="left" @click="prevPage">
			</div>
			<div class="center" @click="toogleTitleAndMenu"></div>
			<div class="right" @click="nextPage">
			</div>
		</div>
	</div>
	<menubar :ifTitleAndMenuShow="ifTitleAndMenuShow" ref="menuBar"
		:fontSizeList="fontSizeList" :defaultFontSize="defaultFontSize"
		@setFontSize="setFontSize" 
		:themeList='themeList' :defaultTheme="defaultTheme"
		@setTheme="setTheme" :bookAvailable="bookAvailable"
		@onProgressChange="onProgressChange"
		@jumpTo="jumpTo" :navigation="navigation"></menubar>
	
</div>
</template>

<script>
	/*引入上下菜单栏组件*/
	import titlebar from '@/components/TitleBar'
	import menubar from '@/components/Menubar'
	
	import Epub from 'epubjs'
	const DOWNLOAD_URL = '/static/英雄志.epub'
    global.ePub = Epub
    export default{
    	//注册组件
    	components:{
    		titlebar,menubar
    	},
    	data(){
    		return{
    			ifTitleAndMenuShow:false,
    			fontSizeList:[
    			{fontSize:12},
    			{fontSize:14},
    			{fontSize:16},
    			{fontSize:18},
    			{fontSize:20},
    			{fontSize:22},
    			{fontSize:24}
    			],
    			defaultFontSize:16,
    			//主题列表
    			themeList:[
    			{
    				name:'默认',
    				style:{
    					body:{
    						'color':'#000','background':'#fff'
    					}
    				}
    			},
    			{
    				name:'护眼',
    				style:{
    					body:{
    						'color':'#000','background':'#ceeaba'
    					}
    				}
    			},
    			{
    				name:'夜间',
    				style:{
    					body:{
    						'color':'#fff','background':'#000'
    					}
    				}
    			}
    			,{
    				name:'经典',
    				style:{
    					body:{
    						'color':'#000','background':'rgb(241,236,226)'
    					}
    				}
    			}],
    			defaultTheme:0,
    		    //图书是否处于可用的状态
    		    bookAvailable:false,
    		    navigation:{}
    		}
    	},
    	methods:{
    		//显示隐藏上下菜单栏
    		toogleTitleAndMenu(){
    			this.ifTitleAndMenuShow = !this.ifTitleAndMenuShow
    			//隐藏A字号设置栏
    			if(!this.ifTitleAndMenuShow){
    				this.$refs.menuBar.hideSetting()    			
    			}
    		},
    		//翻页
    		prevPage(){
    			if(this.rendition){
    				this.rendition.prev();
    			}
    		},
    		nextPage(){
    			if(this.rendition){
    				this.rendition.next();
    			}
    		},
    		/*修改字体大小*/
    		setFontSize(fontSize){
    			this.defaultFontSize=fontSize
    			if(this.themes){
    				this.themes.fontSize(fontSize+'px')
    			}
    		},
    		/*主题*/
    		registerTheme(){
    			this.themeList.forEach(theme=>{
    				this.themes.register(theme.name,theme.style)
    			})
    		},
    		setTheme(index){
    			this.themes.select(this.themeList[index].name)
    			this.defaultTheme = index
    		},
    		/*进度*/
    		//progress 进度条的数值（0-100）
    		onProgressChange(progress){
    			const percentage = progress/100
    			const location = percentage>0?this.locations.cfiFromPercentage(percentage):0
    			this.rendition.display(location)
    		},
    		//根据链接跳转到指定位置
    		jumpTo(href){
    			this.rendition.display(href)
    			this.hideTitleAndMenu()
    		},
    		hideTitleAndMenu(){
    			//隐藏标题菜单栏
    			this.ifTitleAndMenuShow=false
    			//隐藏菜单栏弹出的设置
    			this.$refs.menuBar.hideSetting()
    			//隐藏目录
    			this.$refs.menuBar.hideContent()
    		},
    		
    		//电子书的渲染
    		showEpub (){
    		//生成Book
    		this.book = new Epub(DOWNLOAD_URL);
    		//生成Rendition。 通过Book.renderTo
    		this.rendition = this.book.renderTo('read',{
    			width:window.innerWidth,
    			height:window.innerHeight
    		})
    		//通过Rendtion。display渲染电子书
    		this.rendition.display()
    		//获取Theme对象
    		this.themes=this.rendition.themes
    		//设置默认字体
    		this.setFontSize(this.defaultFontSize)
    		//进度
    		//this.themes.register(name,styles)
    		//this.themes.select(name)
    		this.registerTheme()
    		this.setTheme(this.defaultTheme)
    		//this.themes.select('eye')
    		//获取locations对象
    		//通过epubjs的钩子函数来实现
    		this.book.ready.then(()=>{
    			//目录
    			this.navigation = this.book.navigation
    			console.log(this.navigation)
    			return this.book.locations.generate()
    		}).then(result=>{
    			this.locations = this.book.locations
    			//this.onProgressChange(100)
    			this.bookAvailable = true
    		})
    		//
    		
    		
    		}
    	},
    	mounted (){
    		this.showEpub()
    	}
    }
</script>

<style lang="scss" scoped>
@import 'assets/styles/global';
.ebook{
	position: relative;
	
	/*点击区域*/
	.read-wrapper{
		.mask{
			position: absolute;
			top: 0;
			left: 0;
			z-index: 100;
			display:flex;
			width: 100%;
			height: 100%;
			.left{
				flex: 0 0 px2rem(100);
			}
			.center{
				flex: 1;
			}
			.right{
				flex: 0 0 px2rem(100);
			}
		}
	}
	
}

</style>