#vue+better-scroll组件封装
>实现的功能：

1. 上下拉数据添加和刷新
2. 根据请求的情况更改下面文字的改变
3. 根据自己的ajax的请求情况实现对应的功能

	    <template>
	    <div ref="wrapper" class="list-wrapper">  
	    <div class="scroll-content">   
	    <slot></slot>
	    <div>
	    <div class="pullup" v-show="!inPullUp&&dataList.length>0&&pullUpLoad">{{beforePullUpWord}}</div>
	    <div class="pullup" v-show="inPullUp"><img src="../assets/loading.gif"  alt=""><span>{{PullingUpWord}}</span></div>
	    </div>   
	    </div>  
	    <transition name="pullDown">
	       <div class="pullDown" v-show="inPullDown"><img src="../assets/loading.gif"  alt=""><span>{{PullingDownWord}}</span></div>
	    </transition> 
	    </div>
	    	</template>
	    	<script >
	    	/**
	    	组件使用方法
	    	1.派发加载事件
	    	pullDownRefresh:{threshold:80,stop:40}这是阀值
	    	pullUpLoad:{threshold:-80}
	    	2.$emit触发事件:
	    	@onPullUp="pullUpHandle"
	    	@onPullDown="pullDownHandle"
	    	3.通过监听得到获得scroll对象
	    	computed:{scrollElement(){return this.$refs.scroll}},
	    	4.可以修改文字和结束滚动事件，
	    	continuePullUp=false;
	    	5.数据数组要绑定length大于零。
	    	**/
	    	  import BScroll from 'better-scroll'
	    	  const  PullingUpWord="正在加载中...";
	    	  const  beforePullUpWord="↑↑上拉加载更多";
	    	  const  finishPullUpWord="加载完成";
	    	  const  PullingDownWord="加载中...";
	    	  export default {
	    props: {
	      dataList:{
	    type: Array,
	    default: []
	      },
	      probeType: {
	    type: Number,
	    default: 3
	      },
	      click: {
	    type: Boolean,
	    default: true
	      },   
	      pullDownRefresh: {
	    type: null,
	    default: false
	      },
	      pullUpLoad: {
	    type: null,
	    default: false
	      }
	    },
	    data() {
	    return {  
	    scroll:null,
	    inPullUp:false,
	    inPullDown:false,
	    beforePullUpWord,
	    PullingUpWord,
	    PullingDownWord,
	    continuePullUp:true,
	    continuePullDown:true
	    }
	    },  
	    mounted() {
	    setTimeout(()=>{
	    this.initScroll();
	    
	    this.scroll.on('pullingUp',()=> {
	    if(this.continuePullUp){
	    this.beforePullUp();
	    this.$emit("onPullUp","当前状态：上拉加载");
	      }
	    });
	    
	    this.scroll.on('pullingDown',()=> {
	    if(this.continuePullDown){
	    this.beforePullDown();
	    this.$emit("onPullDown","当前状态：下拉加载更多");
	    }
	    })
	    },20)
	    },
	    methods: {
	    initScroll() {
	    if (!this.$refs.wrapper) {
	    return
	    }
	    this.scroll = new BScroll(this.$refs.wrapper, {
	    probeType: this.probeType,
	    click: this.click,   
	    pullDownRefresh: this.pullDownRefresh,
	    pullUpLoad: this.pullUpLoad,
	    })
	    },
	    beforePullUp(){
	    this.PullingUpWord=PullingUpWord;
	    this.inPullUp=true;
	    }, 
	    beforePullDown(){
	    this.disable();
	    this.inPullDown=true;
	    },
	    finish(type){
	    this["finish"+type]();
	    this.enable();
	    this["in"+type]=false;  
	    },
	    disable() {
	    this.scroll && this.scroll.disable()
	    },
	    enable() {
	    this.scroll && this.scroll.enable()
	    },
	    refresh() {
	    this.scroll && this.scroll.refresh()
	    }, 
	    finishPullDown(){
	    this.scroll&&this.scroll.finishPullDown()
	    },
	    finishPullUp(){
	    this.scroll&&this.scroll.finishPullUp()
	    },  
	    },   
	    watch: {
	    dataList() {
	    this.$nextTick(()=>{
	    this.refresh();   
	    })  
	    }
	    },
	    	  }
	    	
	    	</script>
	    	
	    	<style scoped>
	    	
	    	.list-wrapper{
	    position: absolute;
	    left: 0;
	    top: 0;
	    bottom:0;
	    right:0;
	    overflow: hidden;
	    	}
	    	
	    	.list-content{
	    	  position: relative;
	    	  z-index: 10;
	    	}
	    	.pulldown-wrapper{
	    	position: absolute;
	    	width: 100%;
	    	left: 0;
	    	display: flex;
	    	justify-content :center;
	    	align-items: center;
	    	transition: all;
	    	}
	    	.pullup-wrapper{
	    	width: 100%;
	    	display: flex;
	    	justify-content: center;
	    	align-items: center;
	    	padding: 16px 0;
	    	}
	    	.pullDown-enter-active{
	    	transition:all 0.2s;
	    	}
	    	.pullDown-enter, .pullDown-leave-active{
	    	transform:translateY(-100%);
	    	transition:all 0.2s;
	    	}
	    	.pullDown{
	    	position: absolute;
	    	top:0;
	    	left:0;
	    	width: 100%;
	    	height: 40px;
	    	line-height: 40px;
	    	text-align: center;
	    	}
	    	.pullup{
	    	width: 100%;
	    	height: 40px;
	    	line-height: 40px;
	    	text-align: center;
	    	}
	    	.pullup img,.pullDown img{
	    	vertical-align: middle;
	    	}
	    	</style>