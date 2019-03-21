<template>
	<!--上菜单栏 -->
	<transition name="slide-down">
		<div class="title-wrapper" v-show="ifTitleAndMenuShow">
		<div class="left">
			<span class="icon-back icon"></span>
		</div>
		<div class="right">
			<div class="icon-wrapper">
					<span class="icon-cart icon"></span>
				</div>
				<div class="icon-wrapper">
					<span class="icon-person icon"></span>
				</div>
				<div class="icon-wrapper">
					<span class="icon-more icon"></span>
				</div>
		</div>
	</div>
	</transition>
</template>

<script>
	export default{
		props:{
			ifTitleAndMenuShow:{
				type:Boolean,
				default:false
			}
		}
	}
</script>

<style lang="scss" scoped> 
	@import '../assets/styles/global';
/*上菜单栏*/
	.title-wrapper{
		position: absolute;
		top:0;
		left:0;
		z-index:101;
		width:100%;
		height:px2rem(48);
		display:flex;
		background:white;
		box-shadow: 0 px2rem(8) px2rem(8) rgba(0,0,0,.15);
		.left{
			flex: 0 0 px2rem(60);
			justify-content: flex-start;
		}
		.right{
			flex: 1;
			display: flex;
			justify-content: flex-end;
			icon-wrapper{
				flex: 0 0 px2rem(40);
				@include center;
				.icon-cart{
					font-size: px2rem(22);
				}
			}
		}
		/*过渡动画显示前*/ /*过渡动画隐藏后*/
		&.slide-down-enter,&.slide-down-leave-to{
			/*位移*/
			transform: translate3d(0,-100%,0);
		}
		/*过渡动画显示后*/ /*过渡动画隐藏前*/ /*一般可以不用这两个属性*/
		&.slide-down-enter-to,&.slide-down-leave{
			/*回到原位*/
			transform: translate3d(0,0,0);
		}
		/*过渡动画显示过程*/
		&.slide-down-enter-active,&.slide-down-leave-active{
			/*监听所有的动画0.3s 线性动画*/
			transition: all .3s linear;
		}
	}
</style>