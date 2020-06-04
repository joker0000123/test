<template>
  <!--直播收流-->
  <div class="player-container">
    <video-player
      class="video-player vjs-custom-skin"
      ref="videoPlayer"
      :playsinline="playsinline"
      :options="playerOptions"
      @play="onPlayerPlay($event)"
      @pause="onPlayerPause($event)"
      @ended="playEnded"
      @error="playError"
      @canplay="onPlayerCanplay"
    ></video-player>
  </div>
</template>

<script>
import { videoPlayer } from "vue-video-player";
import "video.js/dist/video-js.css";
import "vue-video-player/src/custom-theme.css";
import "./index.less";
//引入hls.js
import "videojs-contrib-hls.js/src/videojs.hlsjs";
export default {
  props: {
    videoSrc: {
      default: ""
    },
    livePoster: {
      default: ""
    }
  },
  data() {
    return {};
  },
  components: {
    videoPlayer
  },
  computed: {
    playsinline() {
      var ua = navigator.userAgent.toLocaleLowerCase();
      //x5内核
      if (
        ua.match(/tencenttraveler/) != null ||
        (ua.match(/qqbrowse/) != null) != null ||
        ua.match(/websdk/) != null
      ) {
        return false;
      } else {
        //ios端
        return true;
      }
    },
    player() {
      return this.$refs.videoPlayer.player;
    },
    playerOptions() {
      return {
        //playbackRates: [0.7, 1.0, 1.5, 2.0], //播放速度
        autoplay: true, //如果true,浏览器准备好时开始回放。
        controls: true, //控制条
        muted: false, // 默认情况下将会消除任何音频。
        loop: false, // 导致视频一结束就重新开始。
        preload: "auto", // 建议浏览器在<video>加载元素后是否应该开始下载视频数据。auto浏览器选择最佳行为,立即开始加载视频（如果浏览器支持）
        language: "zh-CN",
        //aspectRatio: '16:9', // 将播放器置于流畅模式，并在计算播放器的动态大小时使用该值。值应该代表一个比例 - 用冒号分隔的两个数字（例如"16:9"或"4:3"）
        fluid: true, // 当true时，Video.js player将拥有流体大小。换句话说，它将按比例缩放以适应其容器
        sources: [
          {
            type: "application/x-mpegURL", //"video/mp4",
            //src: "http://r01.wscdn.hls.xiaoka.tv/live/ZNUUUXDBH6hsd-V-/playlist.m3u8" //你的视频地址（必填）
            //src: 'https://al01.alcdn.hls.xiaoka.tv/live/S2LEfUevIEH7tqvU.m3u8',
            src: this.videoSrc
            /* type: 'video/mp4',
					src: 'https://qiniu.waoo.cc/burtyangdouyin1.mp4', */
          }
        ],
        poster: this.livePoster,
        width: document.documentElement.clientWith
        //notSupportedMessage: '此视频暂无法播放，请稍后再试', //允许覆盖Video.js无法播放媒体源时显示的默认信息。
        /* controlBar: {
					timeDivider: true,
					durationDisplay: true,
					remainingTimeDisplay: false,
					fullscreenToggle: true  //全屏按钮
				} */
      };
    }
  },
  mounted() {
    setTimeout(() => {
      var ua = navigator.userAgent.toLocaleLowerCase();
      if (
        ua.match(/tencenttraveler/) != null ||
        ua.match(/qqbrowse/) != null ||
        ua.match(/websdk/) != null
      ) {
        $(".player-container video")
          .attr("x-webkit-airplay", true)
          .attr("x5-playsinline", true)
          .attr("webkit-playsinline", true)
          .attr("playsinline", true);
        this.$refs.videoPlayer.player.play(); //播放
      } else {
        $(".player-container video")
          .attr("webkit-playsinline", "true")
          .attr("playsinline", "true");
        //ios端
        document.addEventListener(
          "WeixinJSBridgeReady",
          function() {
            $(".player-container video")
              .get(0)
              .play();
          },
          false
        );
      }
    }, 3000);
  },
  methods: {
    //在vue-video-player的onPlayerCanplay(视频可播放)这个方法中添加回调
    onPlayerCanplay(player) {
      //解决自动全屏
      var ua = navigator.userAgent.toLocaleLowerCase();
      //x5内核
      if (ua.match(/tencenttraveler/) != null || ua.match(/qqbrowse/) != null) {
        $(".player-container video")
          .attr("x-webkit-airplay", true)
          .attr("x5-playsinline", true)
          .attr("webkit-playsinline", true)
          .attr("playsinline", true);
      } else {
        //ios端
        $(".player-container video")
          .attr("webkit-playsinline", "true")
          .attr("playsinline", "true");
      }
    },
    onPlayerPlay(player) {
      console.log("play");
    },
    onPlayerPause(player) {
      console.log("pause");
    },
    //播放结束
    playEnded() {
      console.log(111);
    },
    playError() {}
  }
};
</script>

<style>
</style>
