<template>
	<transition name="fade">
		<div class="image-view" >
			<div class="img-box" id="box" @touchmove="slideMove($event)" @touchstart="slideStart" @touchend="slideEnd($event)">
				<div class="img-list" :class="{'active':isStart}" :style="{width:fixWidth*imgList.length+'px',transform:'translate3d('+(-(fixWidth*(inIndex-1))+movex+'px'+','+0+','+0+')')}">
					<div v-for="(item,index) in imgList" :style="{width:fixWidth+'px'}"  >
						<img :src="item.src"  >
						<span v-show="item.desc!=''" :class="{'down':hideBar}">{{item.desc}}</span>
					</div>
				</div>
			</div>
			<transition name="barFade">
				<div class="top bar" v-show="!hideBar">
					<div class="left" @click="hideImageView">
						<i class="icon back"></i>
					</div>
					<div class="center">{{inIndex}}&nbsp;{{spacer}}&nbsp;{{imgList.length}}</div>
					<div class="right"></div>
				</div>
			</transition>
			
		</div>
	</transition>
</template>
<script>
export default {
  	name: 'imageView',
  	data(){
	    return{
	        hideBar:false,
	        timeOutEvent:0, //定时器
	        startPos:{}, // 开始触摸的位置
	        endPos:{},  //结束的位置
	        fixWidth:0,
	        movex:0,
	        inIndex:0,
	        isStart:false,
	        lastClickTime :0,
	        clickTimer:0
	    }
	},
    props:{
    	imgList:{
    		type:Array,
    		default:[]
    	},
    	isshow:{
    		type:Boolean,
    		default:false
    	},
    	index:{
    		type:Number,
    		default:1
    	},
    	spacer:{
    		type:String,
    		default:"/"
    	}
    },
  	mounted(){
  		this.$nextTick(transition=>{
  			this.fixWidth=document.getElementById("box").clientWidth
  			this.inIndex=this.index
  			this.inImgList=this.imgList
  			for(let i=0;i<this.inImgList;i++){
  				this.inImgList[i].isEnlarge=false
  			}
  			window.setTimeout(()=>{
  				this.isStart=true
  			},100)
  			
  		})
    },

    methods:{
    	hideImageView(){

    		this.$emit("hideImageView")
    	},
    	isHideBar(){
    		this.hideBar=!this.hideBar
    	},
    	slideStart(e){
    		this.timeOutEvent=setTimeout(()=>{
    			this.timeOutEvent=0
    			console.log("长按")
    		},500);
    		this.startPos={
    			x:e.touches[0].pageX,
    			y:e.touches[0].pageY,
    		}
    	},

    	slideMove(e){
    		clearTimeout(this.timeOutEvent);//如果手指有移动，清除定时器，此时说明用户只是要移动而不是长按
    		this.timeOutEvent = 0;
    		if(e.touches.length>1){
    			return false
    		}
    		this.endPos={
    			x:e.touches[0].pageX,
    			y:e.touches[0].pageY,
    		}

    		if(Math.abs(this.endPos.x-this.startPos.x)<Math.abs(this.endPos.y-this.startPos.y)){
    			return false
    		}

    		this.movex=this.endPos.x-this.startPos.x

    	},

    	slideEnd(e){
    		clearTimeout(this.timeOutEvent);//清除定时器  
    		this.movex=0
    		if(Math.abs(this.endPos.x-this.startPos.x)>this.fixWidth/2) {
    			if((this.endPos.x-this.startPos.x)<0&&this.inIndex<this.imgList.length){
    				this.inIndex++
    			}else if((this.endPos.x-this.startPos.x)>0&&this.inIndex>1){
    				this.inIndex--
    			}
    		}
    	},

    	zoom(target){
    		let nowTime=new Date().getTime()
    		if (nowTime - this.lastClickTime < 400) {
                /*双击*/
                if(target.currentTarget.className=='') target.currentTarget.className='enlarge'
                else target.currentTarget.className=''
                this.lastClickTime = 0;
                this.clickTimer && clearTimeout(this.clickTimer);          
            } else {
                /*单击*/
                this.lastClickTime = nowTime;
                this.clickTimer = setTimeout(() => {
                	this.hideBar=!this.hideBar
                }, 400);
            }
    	},

    }
}
</script>
<style>
.fade-enter-active, .fade-leave-active {
	-webkit-transition: -webkit-transform .4s,opacity .4s;
	-moz-transition: -moz-transform .4s,opacity .4s;
	-o-transition: -o-transform .4s,opacity .4s;
    transition: transform .4s,opacity .4s;
    -webkit-transform: scale(1);
    -moz-transform: scale(1);
    -o-transform: scale(1);
    transform: scale(1);
    opacity: 1;
}
.fade-enter, .fade-leave-to  {
    -webkit-transform: scale(0.5);
    -moz-transform: scale(0.5);
    -o-transform: scale(0.5);
    transform: scale(0.5);
    opacity: 0;
}
.barFade-enter-active, .barFade-leave-active {
	-webkit-transition: opacity .4s;
	-moz-transition: opacity .4s;
	-o-transition: opacity .4s;
    transition: opacity .4s;
    opacity: 1;
}
.barFade-enter, .barFade-leave-to  {
    opacity: 0;
}
.image-view{
	position: absolute;
    left: 0;
    top: 0;
    width: 100%;
    height: 100%;
    z-index: 11500;
    display: -webkit-box;
    display: -ms-flexbox;
    display: -webkit-flex;
    display: flex;
    -webkit-align-items: center;
    -moz-align-items: center;
    -o-align-items: center;
    align-items: center;
}
.bar{
	position: absolute;
	left:0;
	right: 0;
	height: 44px;
    box-sizing: border-box;
    background: rgba(30, 30, 30, 0.8);
    display: -webkit-box;
    display: -ms-flexbox;
    display: -webkit-flex;
    display: flex;
    -webkit-box-pack: justify;
    -ms-flex-pack: justify;
    -webkit-justify-content: space-between;
    justify-content: space-between;
    -webkit-box-align: center;
    -ms-flex-align: center;
    -webkit-align-items: center;
    align-items: center;
    font-size: 17px;
	color: #fff;
	padding: 0 8px;
}
.top{
	top:0;
}
.top .left,.top .right{
	-webkit-flex-shrink: 0;
    -ms-flex: 0 0 auto;
    flex-shrink: 0;
    display: -webkit-box;
    display: -ms-flexbox;
    display: -webkit-flex;
    display: flex;
    -webkit-box-pack: start;
    -ms-flex-pack: start;
    -webkit-justify-content: flex-start;
    justify-content: flex-start;
    -webkit-box-align: center;
    -ms-flex-align: center;
    -webkit-align-items: center;
    align-items: center;
    -webkit-transform: translate3d(0, 0, 0);
    transform: translate3d(0, 0, 0);
}
.top .center{
	margin-left: -11px;
	font-size: 17px;
}
i.icon{
	display: inline-block !important;
	margin: 0px auto 0 auto;
	vertical-align: middle;
    background-size: 100% auto;
    background-position: center;
    background-repeat: no-repeat;
    font-style: normal;
    position: relative;
	width: 12px;
	height: 20px;
	margin-left: 10px
}
.icon.back{
	background-image: url(../assets/back.svg)
}
.img-box{
	height: 100%;
	width: 100%;
	background: #000;
	overflow: hidden;

}
.img-list{
	height: 100%;
	display: -webkit-box;
    display: -ms-flexbox;
    display: -webkit-flex;
    display: flex;
	-webkit-box-align: center;
    -ms-flex-align: center;
    -webkit-align-items: center;
    align-items: center;
}
.img-list.active{
	-webkit-transition: -webkit-transform .4s;
    -moz-transition: -moz-transform .4s;
    -o-transition: -o-transform .4s;
    transition: transform .4s;
}
.img-list >div{
	position:relative;
	height: 100%;
	display: -webkit-box;
    display: -ms-flexbox;
    display: -webkit-flex;
    display: flex;
	-webkit-box-align: center;
    -ms-flex-align: center;
    -webkit-align-items: center;
    align-items: center;
}
.img-list span{
	display: block;
	position:absolute;
	text-align:center;
	left:0;
	right:0;
	bottom:0;
	color:#fff;
	font-size:12px;
	padding:5px 10px;
	background: rgba(0, 0, 0, 0.8);
	-webkit-transition: bottom .4s;
	-moz-transition: bottom .4s;
	-o-transition: bottom .4s;
    transition: bottom .4s;
    line-height:16px;
}
.img-list span.down{
	bottom:0;
}
.img-list img{
	display: block;
	width:100%;
	max-height:100%;
	
}
.img-list .enlarge img{
	-webkit-transform:scale(3);
	-moz-transform:scale(3);
	-o-transform:scale(3);
	transform:scale(3);
}
</style>