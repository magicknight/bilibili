<template>  
    <div class="play_box">
        <div class="index_play">
            <div class="play_container" >
                <div class="play_box_l">
                    <video  
                        width="100%" 
                        height="100%" 
                        preload="auto" 
                        ref="video"
                        load="loaded"
                        webkit-playsinline
                        playsinline
                        v-show="videoflag"
                        @click="toggleVideo"
                        @timeupdate="update"
                        @ended="end"
                    >
                    </video>
                    <div class="display">
                        <div class="input-bar"></div>
                        <div class="control-bar" v-show="barshow">
                            <div class="time-container" ref="timebox">
                                <span class="control-text">{{getTime(currentTime)}}</span>
                                <span class="control-split"></span>
                                <span class="time-total">{{getTime(player.duration)}}</span>
                            </div>
                            <div class="right">
                                <span class="btn-comment">
                                    <i class="icon-comment"></i>
                                </span>
                                <span class="control-btn">
                                    <i class="icon-widescreen"></i>
                                </span>
                            </div>
                            <!-- 进度条 -->
                            <pross-bar :precent="precent" @precentChange="precentChange" :timeWidth="timeWidth"></pross-bar>
                        </div>
                        <div class="load-layer">   
                            <img :src="player.pic" v-show="show">
                            <!-- 按钮 -->
                            <i  v-show="btnshow" :class="getIcion" 
                                @click="toggleClick"
                                ></i>

                            <div class="index_top" v-show="show">
                                <p>av{{player.aid}}</p>
                            </div>
                            <div class="index_time_item" v-show="show">
                                <p>{{getTime(player.duration)}}</p>
                            </div>
                            <div class="index_cccTpis" v-show="show">
                                <div class="index_banner" >
                                    <p>高清更流畅，就用bilibili客户端(*^_^*)</p>
                                </div>
                                <div class="open_link">
                                    <p>用客户端打开</p>
                                </div>
                                <div class="index_clear"></div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <div class="index_opera">
            <div class="index_operabtn">
                <div class="index_showfiexld">
                    <div class="index_icon">
                        <img src="./share.png" alt="">
                    </div>
                    <p>分享</p>
                </div>
            </div>
            <div class="index_operabtn">
                <div class="index_showfiexld">
                    <div class="index_icon">
                        <img src="./collect.png" alt="">
                    </div>
                    <p>收藏</p>
                </div>
            </div>
            <div class="index_operabtn">
                <div class="index_showfiexld">
                    <div class="index_icon">
                        <img src="./download.png" alt="">
                    </div>
                    <p>缓存</p>
                </div>
            </div>
        </div>
        <div class="index_videoinfo">
            <h1 class="index_title">{{player.title}}</h1>
            <div class="index_info">
                <i class="index_play_icon"></i>
                    <span class="inddex_play_txt" v-if="player.stat">{{player.stat.view }}</span>
                <i class="index_dmicon"></i>
                <span class="inddex_dm_txt" v-if="player.stat">{{player.stat.danmaku}}</span>
                <i class="index_flagbtn"  :class="{'active':iconbtn}" @click="iconReges"></i>
            </div>
            <div class="index_descWrap" ref="pageWrap" id="pagewrap">
                <div class="index_desc_videopage" ref="pageVideo">
                    <span>
                        {{player.description ? player.description : player.desc}}
                    </span>
                </div>
            </div>
        </div>
        <!-- 视频相关 -->
        <video-list :videoList="videoList" @onTop="onTop"></video-list>
        <!-- 评论 -->
        <commint-list :commintList="commintList"></commint-list>
        <!-- 底部 -->
        <play-btn @onTop="onTop"></play-btn>
    </div>
</template>
<script>
    import ProssBar from 'base/pross-bar/pross-bar'
    import {getPlayer,getCommint} from 'api/player'
    import PlayBtn from 'base/playbtn/playbtn'
    import CommintList from 'base/commintlist/commintlist'
    import {mapGetters} from 'vuex'
    import VideoList from 'base/videolist/videolist'
    import {VideoListData} from 'api/videoList.js'
    
    export default{
        data(){
            return {
                timeWidth:0,
                dataList:{},
                src:'',
                showplay:true,
                currentTime:0,
                icon:false,
                url:true,
                show:true,
                btnshow:true,
                barshow:false,
                index:1,
                videoflag:false,
                iconbtn:false,
                videoList:[],
                commintList:[]
            }
        },
        created(){
            this.videoList = VideoListData.data
            this.getPlayData()
            this.getCommintDate()
        },
        computed:{
            precent:function(){
                return this.currentTime / this.player.duration
            },
            getIcion:function(){
                if(!this.icon){
                    return 'pause-icon'
                }else{
                    return 'player-icon'
                }
            },
            ...mapGetters([
                'player'
            ])
        },
        methods:{
            getCommintDate(){
                getCommint(this.player.aid).then((res)=>{
                    this.commintList = res.data.replies
                })
            },
            onTop(){
                document.body.scrollTop = 0
            },
            toggleClick(){
                if(this.url){
                    this.$refs.video.src = this.src
                    this.url = false
                }
                this.show = false
                this.barshow = true
                this.icon = !this.icon
                this.videoflag = true
            },
            iconReges(){
                this.iconbtn = !this.iconbtn
                this.heightY = '300px'
            },
            precentChange(precent){
                if(this.$refs.video){
                    this.$refs.video.currentTime = this.player.duration * precent ? this.player.duration * precent : 0
                }
            },
            toggleVideo(){
                this.btnshow = !this.btnshow
                this.barshow = !this.barshow
            },
            _pop(num){
                const len = num.toString().length
                if(len < 2){
                    return '0' + num
                }
                return num
            },
            getPlayData(){
                getPlayer(this.player.aid).then((res)=>{
                    this.src = res.durl[0].url
                    this.dataList = res
                })
            },
            update(e){
                this.currentTime =  e.target.currentTime
            },
            end(){
                this.icon = false
            },
            getTime:function(inter){
                inter = inter | 0
                const muinte = inter / 60 | 0
                const second = this._pop(inter % 60)
                return `${muinte}:${second}`
            }       
        },
        watch:{
            src(newVal){
                this.$refs.video.src = newVal
            },
            player(newVal){
                this.getPlayData()
                this.getCommintDate()
                this.barshow = false
                this.show = true
                this.btnshow = true
                this.videoflag = false
                this.icon = false
            },
            icon(newY){
                if(newY){
                    this.$refs.video.play()
                }else{
                    this.$refs.video.pause()
                }
            },
            currentTime(){
                this.timeWidth = this.$refs.timebox.clientWidth
            },
            iconbtn(flag){
                if(flag){
                    document.querySelector('#pagewrap').style.height = this.$refs.pageVideo.clientHeight +'px'
                }else{
                    document.querySelector('#pagewrap').style.height = '20' + 'px'
                }
            }
        },
        components:{
            ProssBar,
            VideoList,
            CommintList,
            PlayBtn
        }
    }
</script>
<style lang="stylus" rel="stylesheet/stylus">
    .play_box
        position:absolute;
        left:0
        right:0
        top:0
        bottom:0
        z-index:15
        .llx
            height: 100%;
            overflow: hidden;
        .index_videoinfo
            padding: .832rem .512rem 0;
            border-bottom: .02133rem solid #ccc;
            background: #f3f3f3;
            .index_title
                margin: 0 0 .49067rem;
                line-height: 1.024rem;
                font-size: .64rem;
                font-weight: 500;
                color: #212121;
            .active
                height:200px;
            .index_descWrap
                height: .85333rem;
                margin-bottom: 1.024rem;
                overflow: hidden;
                transition: all .5s;
                .index_desc_videopage
                    line-height: .85333rem;
                    color: #757575;
                    font-size: .512rem;
                    word-break: break-all;
                    span
                        line-height: .85333rem;
                        color: #757575;
                        font-size: .512rem;
                        word-break: break-all;
            .index_info
                position: relative;
                margin-bottom: .46933rem;
                color: #757575;
                .index_play_icon
                    display: inline-block;
                    margin-right: .21333rem;
                    vertical-align: middle;
                    width: .704rem;
                    height: .53333rem;
                    overflow: hidden;
                    background: url(./ui_2.png) no-repeat;
                    background-size: 15.36rem 46.72rem;
                    background-position: -6.37867rem -3.2rem;
                    margin-left:0.5rem;
                .inddex_play_txt
                    display: inline-block;
                    height: .53333rem;
                    width: 3.13333rem;
                    vertical-align: middle;
                    line-height: .53333rem;
                    font-size: .512rem;
                    color: #757575;
                    margin-left:-0.1rem;
                    margin-top:-0.07rem;
                .index_dmicon
                    display: inline-block;
                    margin-right: .21333rem;
                    vertical-align: middle;
                    width: .704rem;
                    height: .53333rem;
                    overflow: hidden;
                    background: url(./ui_2.png) no-repeat;
                    background-size: 15.36rem 46.72rem;
                    background-position: -5.12rem -3.2rem;
                .inddex_dm_txt
                    display: inline-block;
                    height: .53333rem;
                    width: 2.13333rem;
                    vertical-align: middle;
                    line-height: .53333rem;
                    font-size: .512rem;
                    color: #757575;
                    margin-left:-0.1rem;
                    margin-top:-0.07rem;
                .index_flagbtn
                    position: absolute;
                    right: 0;
                    top: 0;
                    bottom: 0;
                    margin: auto 0;
                    width: .74667rem;
                    height: .53333rem;
                    background: url(//static.hdslb.com/mobile/img/ui_2.png) no-repeat;
                    background-size: 15.36rem 46.72rem;
                    background-position: -5.29067rem -6.10133rem;
                    transition: all .1s;
                .active
                    background-position: -5.29067rem -7.36rem;
        .index_opera
            width: 100%;
            background-color: #fff;
            overflow:hidden;
            border-bottom: .02133rem solid #ccc;
            margin-top:2rem;
            .index_operabtn
                position: relative;
                width: 33.33%;
                height: 2.048rem;
                float: left;
                .index_showfiexld
                    position: absolute;
                    width: 2.73067rem;
                    height: 50%;
                    top: 50%;
                    left: 50%;
                    transform: translate(-50%,-50%);
                    -webkit-transform: translate(-50%,-50%);
                    .index_icon
                        position: relative;
                        float: left;
                        width: 37.5%;
                        img
                            display: block;
                            width: 100%;
                    p
                        position: relative;
                        float: right;
                        line-height: 1.024rem;
                        color: #999;
                        font-size: .55467rem;
                        text-align: right;
        .index_play
            position: relative;
            z-index: 100;
            top: 2rem;
            height:10rem;
            .play_container
                position: relative;
                display: inline-block;
                width: 100%;
                height: 100%;
                z-index:10;
                background:#fff;
                overflow:hidden;
                .play_box_l
                    overflow:hidden;
                    video
                        width:100%;
                        height:100%;
                        position:absolute;
                        z-index:8
                    .display
                        position:absolute;
                        left:0;
                        right:0;
                        top:0;
                        bottom:0;
                        overflow:hidden;
                        background:#000;
                        .control-bar
                            position: absolute;
                            z-index: 10;
                            bottom: 0;
                            left: 0;
                            right: 0;
                            height: 1.86655rem;
                            border-width: 0;
                            border-style: solid;
                            border-color: #e2e2e2;
                            background-color: rgba(0, 0, 0, 0.5);
                            font-size: 0;
                            text-align: left;
                            opacity: 1;
                            .right
                                height: 100%;
                                float: right;
                                font-size: 0;
                                cursor: default;
                                display: inline-block;
                                .control-btn
                                    font-size: 1rem;
                                    display: inline-block;
                                    height: 1.86655rem;
                                    text-align: center;
                                    vertical-align: top;
                                    background-color: transparent;
                                    color: #585858;
                                    width: 1.664rem;
                                    margin-left: 0.59733rem;
                                    .icon-widescreen
                                        list-style: none;
                                        cursor: default;
                                        height: 100%;
                                        content: '';
                                        display: block;
                                        background: transparent url(./ui_2.png) no-repeat;
                                        background-position:-3.30027rem -16.12416rem;
                                        background-size: 19.968rem 60.76373rem;
                                .btn-comment
                                    color: #ccc;
                                    font-size: 1rem;
                                    display: inline-block;
                                    height: 1.86655rem;
                                    text-align: center;
                                    vertical-align: top;
                                    background-color: transparent;
                                    color: #585858;
                                    width: 1.664rem;
                                    margin-left: 0.59733rem;
                                    .icon-comment
                                        list-style: none;
                                        cursor: default;
                                        height: 100%;
                                        content: '';
                                        display: block;
                                        background: transparent url(./ui_2.png) no-repeat;
                                        background-position:-4.7424rem -16.08533rem;
                                        background-size: 19.968rem 60.76373rem;
                            .time-container
                                position:absolute;
                                height: 1.86655rem;
                                overflow:hidden;
                                .control-text
                                    width: 2.1332rem;
                                    text-align: right;
                                    font-size: 0.66662rem;
                                    font-family: arial,sans-serif;
                                    color: #fff;
                                    height: 100%;
                                    line-height: 2.06653rem;
                                    vertical-align: top;
                                    float: left;
                                .control-split
                                    width: 0.33331rem;
                                    font-size: 0.66662rem;
                                    font-family: arial,sans-serif;
                                    color: #fff;
                                    height: 100%;
                                    line-height: 2.06653rem;
                                    vertical-align: top;
                                    float: left;
                                    &::before
                                        content: "/";
                                        margin: 0 0.06666rem;
                                .time-total
                                    text-align: left;
                                    width: 2.1332rem;
                                    font-size: 0.66662rem;
                                    font-family: arial,sans-serif;
                                    color: #fff;
                                    height: 100%;
                                    line-height: 2.06653rem;
                                    vertical-align: top;
                                    float: left;
                        .load-layer
                            position:absolute;
                            top: 0;
                            bottom: 0;
                            left: 0;
                            right: 0;
                            display: block;
                            overflow: hidden;
                            cursor: pointer;
                            text-align: center;
                            img
                                border-style: none;
                                display: inline-block;
                                position: relative;
                                width: 100%;
                                height: 100%;
                                opacity: 0.85;
                                margin: 0;
                                padding: 0;
                                // filter: blur(0.34133rem);
                                -webkit-filter: blur(0.34133rem);
                            .pause-icon
                                position: absolute;
                                display: block;
                                bottom: 1.79988rem;
                                right: 0.06666rem;
                                width: 3.18933rem;
                                height: 2.69013rem;
                                background: transparent url(./ui_2.png) no-repeat -3.38347rem -10.03947rem;
                                background-size: 19.968rem 60.76373rem;
                                z-index:10
                            .player-icon
                                position: absolute;
                                display: block;
                                bottom: 1.79988rem;
                                right: 0.06666rem;
                                width: 3.18933rem;
                                height: 2.69013rem;
                                background-repeat: no-repeat;
                                background: transparent url(./ui_2.png) no-repeat -3.38347rem -13.36747rem;
                                background-size: 19.968rem 60.76373rem;
                                z-index:10
                            .index_top
                                position: absolute;
                                top: 0;
                                left: 0;
                                z-index: 999;
                                width: 100%;
                                background: -webkit-gradient(linear,0 0,0 100%,from(rgba(33,33,33,.5)),to(rgba(33,33,33,0)));
                                background: linear-gradient(180deg,rgba(33,33,33,.5),rgba(33,33,33,0));
                                p
                                    font-size: .64rem;
                                    color: #fff;
                                    line-height: 1.36533rem;
                                    text-align: center;
                            .index_time_item
                                position: absolute;
                                z-index: 999;
                                bottom: 2.56rem;
                                left: .53333rem;
                                border: .08533rem solid hsla(0,0%,100%,.6);
                                background-color: hsla(0,0%,100%,.2);
                                padding-left: .17067rem;
                                padding-right: .17067rem;
                                border-radius: .21333rem;
                                p
                                    color: #fff;
                                    font-size: .64rem;
                                    line-height: .98133rem;
                                    text-align: center;
                            .index_cccTpis
                                position: absolute;
                                z-index: 999;
                                width: 100%;
                                height: 1.62133rem;
                                left: 0;
                                bottom: 0;
                                background-color: rgba(33,33,33,.5);
                                .index_banner
                                    position: relative;
                                    float: left;
                                    margin-left: .512rem;
                                    p
                                        font-size: .55467rem;
                                        line-height: 1.62133rem;
                                        color: #fff;
                                        text-align: left;
                                .open_link
                                    position: relative;
                                    float: right;
                                    background-color: #fb7299;
                                    border-radius: .17067rem;
                                    width: 3.84rem;
                                    height: 1.28rem;
                                    margin-top: .17067rem;
                                    margin-right: .512rem;
                                    p
                                        font-size: .55467rem;
                                        line-height: 1.28rem;
                                        color: #fff;
                                        text-align: center;
                                .index_clear
                                    clear: both;

</style>