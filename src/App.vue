<template>
  <div id="app">
    <div id="container">
      <div id="time-tilte">
        <div id="onShow" class="onShow">
          <div id="groupNum" style="display: inline">{{groupNum[groupNumIndex]}}</div>
          <div style="display: inline">
            团支部<div v-if="ppart==0" style="display: inline">展示</div><div v-else-if="ppart==1" style="display: inline">问答</div>环节
          </div>
        </div>
        <div id="readyShow" class="readyShow" v-if="groupNumIndex<groupNum.length-1">
          <div style="display: inline">请</div>
          <div id="readyGroupNum" style="display: inline">{{groupNum[groupNumIndex+1]}}</div>
          <div style="display: inline">团支部做好准备</div>
        </div>
        <div id="question" class="question" style="display:none"><br/><br/><br/><br/></div>
        <div id="clock"  class="page-title-time" >{{this.sm + ":" + this.ss}}</div>
        <div class="row buttonsLine" style="">
          <el-button type="primary" id="startB" v-on:click="run()" :disabled="disStart">开始</el-button>
          <el-button type="primary" id="endB" v-on:click="stop()" :disabled="disEnd">暂停</el-button>
          <el-button type="success" id="resetB" v-on:click="reset()">重置</el-button>
          <el-button type="info" id="q&aB" v-on:click="Q_A()">{{ppart==0? "Q&A": "展示"}}</el-button>
          <el-button type="primary" id="lastGroupB" v-on:click="goLast" :disabled="groupNumIndex==0" plain>上一个</el-button>
          <el-button type="primary" id="nextGroupB" v-on:click="goNext" :disabled="groupNumIndex==groupNum.length-1" plain>下一个</el-button>
<!--          <input id="startB" class="col-md-2 btn btn-success" type="button" value="Start" onclick="run()">-->
<!--          <input id="endB" class="col-md-2 btn btn-default" type="button" value="Pause" onclick="stop()">-->
<!--          <input id="resetB" class="col-md-2 btn btn-info" type="button" value="Reset" onclick="reset()">-->
<!--          <input id="q&aB" class="col-md-2 btn btn-success" type="button" value="Q&A" onclick="Q_A()">-->
<!--          <input id="lastGroupB" class="col-md-2 btn btn-warning" disabled="disabled" type="button" value="LastG" onclick="lastGroup()">-->
<!--          <input id="nextGroupB" class="col-md-2 btn btn-danger" type="button" value="NextG" onclick="nextGroup()">-->
          <input id="diff" type="hidden">
          <input id="next" type="hidden">
          <!--div><div-->
        </div>
      </div>
    </div>
    <audio controls ref="aud1m" hidden>
      <source src="./assets/mp3/1m.mp3" />
    </audio>
    <audio controls ref="aud5s" hidden>
      <source src="./assets/mp3/5s.mp3" />
    </audio>
  </div>
</template>

<script>
export default {
    name: 'App',
    data(){
      return {
          groupNum: ['040156','026161','031156','051156','144152','081161','015151'],
          groupNumIndex: 0,
          ppart: 0, //0展示环节，1问答环节
          normalelapse: 100,
          disStart: false,
          disEnd: true,
          sms: "00",
          ss: "00",
          sm: "15",
          isQ_A: 0,
          isFiveSecond: 0,
          isOneMinute: 0,
          nextelapse: 100,
          counter: 0,
          startTime: null,
          start: "15:00:00",
          finish: "00:00:00",
          timer: null,
          nowTime: null,
      }
    },
    computed: {
    },
    mounted() {
    },
    methods: {
        run:function () {
            this.disStart = true
            this.disEnd = false;
            this.counter = 0;
            this.startTime = new Date().valueOf();
            this.timer = window.setInterval(this.onTimer, this.nextelapse);
            // this.timer = window.setTimeout(this.onTimer(), 1000)
        },
        stop:function () {
            this.disStart = false;
            this.disEnd = true;
            window.clearTimeout(this.timer);
        },
        reset() {
            this.stop();
            window.clearInterval(this.timer);
            this.normalelapse = 100;
            this.nextelapse = this.normalelapse;
            this.startTime=null;
            if (this.ppart===0) {
                this.sm = "15";
                this.ss = "00";
                this.sms = "00";
                this.start = "15:00:00";
            } else {
                this.sm = "05";
                this.ss = "00";
                this.sms = "00";
                this.start = "05:00:00";
            }
            this.counter = 0;
            this.timer = null;
            this.isOneMinute=0;
            this.isFiveSecond=0;
        },
        Q_A() {
            if (this.ppart===0)
            {
                this.ppart=1;
            }
            else
            {
                this.ppart=0;
            }
            this.reset();
        },
        goLast(){
            this.groupNumIndex--;
            this.ppart=0;
            this.reset();
        },
        goNext(){
            this.groupNumIndex++;
            this.ppart=0;
            this.reset();
        },
        onTimer: function() {
            if (this.start === this.finish) {
                window.clearInterval(this.timer);
                return;
            }
            // debugger

            let hms = String(this.start).split(":");
            let ms = Number(hms[2]);
            let s = Number(hms[1]);
            let m = Number(hms[0]);
            // if (ms === 0) console.log("start:"+this.start)
            if (m<2 && s<3 && this.isOneMinute===0) {
                this.$refs.aud1m.currentTime = 0;//从头开始播放
                this.$refs.aud1m.play(); //播放
                this.isOneMinute=1;
                console.log("1m"+m+':'+s+':'+ms);
            }
            if (m<1 && s<5 && this.isFiveSecond===0) {
                this.$refs.aud5s.currentTime = 0;//从头开始播放
                this.$refs.aud5s.play(); //播放
                this.isFiveSecond=1;
                console.log("5s"+m+':'+s+':'+ms);
            }
            //if (m.isEqual(0) && s.isEqual(55)) alert("55s");
            ms -= 10;
            if (ms < 0) {
                ms = 90;
                s -= 1;
                if (s < 0)
                {
                    s = 59;
                    m -= 1;
                }
            }
            this.sms = ms < 10 ? ("0" + ms) : ms;
            this.ss = s < 10 ? ("0" + s) : s;
            this.sm = m < 10 ? ("0" + m) : m;
            this.start = this.sm + ":" + this.ss + ":" + this.sms;
            window.clearInterval(this.timer);
            // window.clearInterval(this.timer);
            this.counter++;
            let counterSecs = this.counter * 100;
            this.nowTime = new Date().valueOf()
            let elapseSecs = this.nowTime - this.startTime;
            let diffSecs = counterSecs - elapseSecs;
            this.nextelapse = this.normalelapse + diffSecs;
            // diff.value = counterSecs + "-" + elapseSecs + "=" + diffSecs;
            // next.value = "nextelapse = " + nextelapse;
            if (this.nextelapse < 0) this.nextelapse = 0;
            // if (ms === 0) {
            //     console.log(new Date().valueOf())
            //     console.log("counterSecs:", counterSecs)
            //     console.log("nowTime:", this.nowTime)
            //     console.log("startTime:", this.startTime)
            //     console.log("elapseSecs:", elapseSecs)
            //     console.log("diffSecs:", diffSecs)
            //     console.log("nextelapse:", this.nextelapse)
            // }
            this.timer = window.setInterval(this.onTimer, this.nextelapse);
            // this.timer = window.setInterval(this.onTimer(), 100);
        }
    }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
.page-title-time {
  margin-top: 10%;
  position: relative;
  width:80%;
  margin-left: auto;
  margin-right: auto;
  text-align: center;
  letter-spacing: 10px;
  font-weight: 700;
  font-size:120px;
  font-family: fzGBK;
  text-shadow: 0px 0px 40px #E6E6FA;
  color:  black;
}
#web_bg{
  position:fixed;
  top: 0;
  left: 0;
  width:100%;
  height:100%;
  min-width: 1000px;
  z-index:-10;
  zoom: 1;
  background-color: #fff;
  background-repeat: no-repeat;
  background-size: cover;
  -webkit-background-size: cover;
  -o-background-size: cover;
  background-position: center 0;
}
.onShow
{
  width:70%;
  margin-top: 9%;
  margin-left: auto;
  margin-right: auto;
  text-align: center;
  letter-spacing: 10px;
  font-weight: 800;
  font-size:40px;
  font-family: fzGBK;
  text-shadow: 0px 0px 40px #E6E6FA;
  color:  black;
}
.readyShow
{
  margin-top: 1%;
  margin-right: 0px;
  text-align: center;
  letter-spacing: 10px;
  font-weight: 700;
  font-size:30px;
  font-family: fzGBK;
  text-shadow: 0px 0px 40px #E6E6FA;
  color:  black;
}
.question
{
  position:relative;
  margin-top:10%;
  width:70%;
  margin-left: auto;
  margin-right: auto;
  letter-spacing: 10px;
  font-weight: 900;
  font-size:30px;
  font-family: fzGBK;
  text-shadow: 0px 0px 40px #E6E6FA;
  color:  black;
}
.buttonsLine{
  font-weight:bold;
  font-size:30px;
  color:#FF4500;
  position:relative;
  imargin-bottom:5%;
  display: flex;
  justify-content: center;
}
</style>
