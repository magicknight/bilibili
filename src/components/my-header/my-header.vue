<template>
	<div class="header">
		<div class="logo" @click="backHome">
			<img src="./logo.png">
		</div>
		<div class="search" @click="search">
			 <svg class="search-icon" aria-hidden="true">
                <use xlink:href="#icon-sousuo"></use>
            </svg>
		</div>
		<a href="javascript:;" class="history" @click="onSpace"></a>
		<a href="http://d.bilibili.com/download_app.html?preUrl=http%3A%2F%2Fm.bilibili.com%2Fspace" class="donlown"><p>下载客户端</p></a>
	</div>
</template>
<script>
	import {mapMutations} from 'vuex'
	export default{
		methods:{
			backHome(){
				this.$router.push('recommend')
				this.setClsId(0)
				this.settab(true)
			},
			onSpace(){
				this.settab(false)
				this.$router.push('space')
			},
			search(){
				this.setSearchShow(true)
			},
			...mapMutations({
                setClsId:'SET_CLSID',
				setSearchShow:'SET_SEARCH_SHOW',
				settab:'SET_TAB'
            })
		}
	}
</script>
<style lang="stylus" rel="stylesheet/stylus">
@import "~common/stylus/variable"
	
.header
	position: fixed
	z-index: 20
	width:100%
	height:1.32267rem
	padding:.27733rem 0
	background-color: #fff
	.logo
		position:absolute
		display:block
		left:.77867rem
		top:50%
		width:2.64533rem
		-webkit-transform:translateY(-50%);
		img
			position:relative
			display:block
			width:100%
			margin:0 auto
	.search
		position: absolute;
		display: block;
		width: .896rem;
		height: .896rem;
		right: 42%;
		top: 50%;
		-webkit-transform:translateY(-50%);
		.search-icon
			display:block
			width:100%
			height:100%
			fill:#aaa
	.history
		position: absolute;
		display: block;
		width: 1.024rem;
		height: 1.024rem;
		right: 30.13%;
		top: 50%;
		border-radius: 50%;
		-webkit-transform: translateY(-50%);
		background: url(./noface.gif) no-repeat;
		background-size: 100% 100%;
		overflow: hidden;
	.donlown
		position: absolute;
		display: block;
		top: 50%;
		-webkit-transform: translateY(-50%);
		right: .42667rem;
		width: 3.41333rem;
		height: 1.024rem;
		border: .02133rem solid #fb7299;
		border-radius: .17067rem;
		background-color: #fb7299;
		p
			font-size: .55467rem;
			line-height: 1.06667rem;
			text-align: center;
			color: #fff;
	    
</style>