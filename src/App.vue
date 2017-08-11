<template>
  <div id="app">
    <div>
      <img v-for="item in imgList" :src="item.src" @click="view(item)">
      <imageView 
       :imgList="imgList" 
       :index="index"
       @hideImageView="hideImageView" v-if="isshow"></imageView>
    </div>
  </div>
</template>

<script>
import imageView from './components/image'
export default {
  name: 'app',
  components:{
    imageView
  },
  data(){
    return{
        imgList:[
          {src:"https://card.sg.c3i.link:50012/marketevent//File/Image/05f430fa-9675-41ea-ba64-80d521aaac2d.jpg",desc:"在事件处理程序中调用 "},
          {src:"https://card.sg.c3i.link:50012/marketevent//File/Image/30cd9395-7d4d-4782-b04d-daf7642a16f7.jpg",desc:""},
        ],
        isshow:false,
        index:1,
    }
  },
  methods:{
    view(str){
      for(let i=0;i<this.imgList.length;i++){
        if(str==this.imgList[i]){
          this.index=i+1
          this.isshow=true
        }
      }
    },
    hideImageView(){
      this.isshow=false
    },
  }
}
</script>

<style>
html{
  height:100%;
  width:100%;
  background:#ededed; 
}
body {
  margin:0;
  padding:0;
  overflow:hidden;
  height:100%;
  width: 100%
}
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  height:100%;
  overflow:auto;
}
#app >div{
}
img{
  width: 80%;
  margin:0 auto;
}
</style>
