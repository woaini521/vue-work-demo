/* videojs视频播放 */
<template>
  <div class="videojs-box">
    <div class="video-box vertical-horizontal-center" >
      <video-player
        class="vjs-custom-skin video-player"
        ref="videoPlayer"
        :options="playerOptions"
        :playsinline="true"
        customEventName="customstatechangedeventname"
        @ready="playerReadied($event)"
        @loadeddata="onPlayerLoadeddata($event)"
        @canplay="onPlayerCanplay($event)"
        @canplaythrough="onPlayerCanplaythrough($event)"
        @play="onPlayerPlay($event)"
        @playing="onPlayerPlaying($event)"
        @timeupdate="onPlayerTimeupdate($event)"
        @pause="onPlayerPause($event)"
        @waiting="onPlayerWaiting($event)"
        @ended="onPlayerEnded($event)"
        @statechanged="playerStateChanged($event)"
      ></video-player>
    </div>
    <button @click="playMP4()">播放MP4</button>
  </div>
</template>

<script>
import { videoPlayer } from 'vue-video-player';
export default {
    name: 'Videojs-play',
    components: {videoPlayer},
    inject: ['reload'],
    filters: {},
    data() {
        return {
            playerOptions: {
                techOrder: ['html5', 'flash'], // 兼容顺序,使用flash播放，可以播放flv格式的文件
                playbackRates: [0.7, 1.0, 1.5, 2.0], // 播放速度
                autoplay: true, // 如果true,浏览器准备好时开始回放
                notSupportedMessage: '此视频暂无法播放!', // 无法播放时显示的信息
                sourceOrder: true,
                loop: false, // 导致视频一结束就重新开始。
                // preload: 'auto', // 建议浏览器在<video>加载元素后是否应该开始下载视频数据。auto浏览器选择最佳行为,立即开始加载视频（如果浏览器支持）
                muted: true, // 默认情况下将会消除任何音频。
                language: 'zh-CN',
                // aspectRatio: '16:9', // 将播放器置于流畅模式，并在计算播放器的动态大小时使用该值。值应该代表一个比例 - 用冒号分隔的两个数字（例如"16:9"或"4:3"）
                fluid: false, // 当true时，Video.js player将拥有流体大小。换句话说，它将按比例缩放以适应其容器。
                hls: true, // 启用hls？
                flash: {
                    hls: { withCredentials: false }
                },
                html5: { hls: { withCredentials: false } },
                sources: [
                    // {
                    //     type: 'application/x-mpegURL',
                    //     src: 'http://ivi.bupt.edu.cn/hls/cctv1hd.m3u8'
                    // },
                    // {
                    //     type: 'application/x-mpegURL',
                    //     src: 'http://172.19.82.247/vod/2.mp4'
                    // },
                    // {
                    //     // withCredentials: true，
                    //     type: 'application/x-mpegURL', // hls
                    //     src: 'https://d2zihajmogu5jn.cloudfront.net/bipbop-advanced/bipbop_16x9_variant.m3u8' // url地址
                    // }
                    // {
                    //     type: 'rtmp/flv', // rtmp
                    //     src: `rtmp://live.hkstv.hk.lxdns.com/live/hks`// rtmp
                    // }
                    {
                        type: 'rtmp/flv', // rtmp
                        src: `rtmp://58.200.131.2:1935/livetv/hunantv`
                    }
                    // {
                    //     type: 'video/mp4', // 资源格式写法：'video/mp4'，否则控制台会出现notSupportedMessage设置的错误
                    //     src: 'https://cdn.theguardian.tv/webM/2015/07/20/150716YesMen_synd_768k_vp8.webm' // url地址
                    // }
                ],
                poster: '/static/images/author.jpg', // 你的封面地址
                // width: document.documentElement.clientWidth,
                // height: document.documentElement.clientHeight, // 设置高度，fluid需要设置成flase
                // 配置控制栏
                controlBar: {
                    volumePanel: {
                        inline: false // 音量调节是否水平
                    },
                    currentTimeDisplay: true, // 当前播放位置
                    timeDivider: true, // 时间分割线
                    durationDisplay: true, // 总时间
                    progressControl: true, // 进度条
                    remainingTimeDisplay: false, // 剩余时间
                    fullscreenToggle: true // 全屏按钮
                }
            }
        };
    },
    computed: {
        player() {
            return this.$refs.videoPlayer.player;
        }
    },
    watch: {

    },
    mounted() {
        console.log('当前videojs对象', this.player);

    },
    methods: {
        // 按钮点击-取消/关闭
        close() {
            console.log('video close');
            this.$emit('close');
        },

        // 播放
        playMP4() {
            this.playerOptions.sources = [
                {
                    type: 'video/mp4',
                    src: 'https://cdn.theguardian.tv/webM/2015/07/20/150716YesMen_synd_768k_vp8.webm'
                }
            ];
            this.player.play();
        },

        // --------------------视频播放-开始-------------------- */

        // 初始化话播放-在onPlayerCanplay中调用
        initPlay() {
            console.log('initPlay>当前视频播放器实例对象', this.player);
            this.player.play();
        },

        /* 视频播放 */
        // 视频准备完毕
        playerReadied(player) {
            console.log('Readied>视频准备完毕!', player);
        },

        // 视频加载完成
        onPlayerLoadeddata(player) {
            console.log('Loadeddata>视频加载完成!', player);
        },

        // 可以播放视频
        onPlayerCanplay(player) {
            console.log('Canplay>可以播放视频!', player);
            this.initPlay(player);
        },

        // 拖动视频条会触发事件-视频暂停>可以播放视频>可以播放视频至拖动位置>视频播放>视频加载中>可以播放视频>视频播放中>视频加载中>可以播放视频>视频播放中>视频加载中>可以播放视频>视频播放中>可以播放视频至拖动位置
        // 可以播放视频至拖动位置
        onPlayerCanplaythrough(player) {
            console.log('Canplaythrough>可以播放视频至拖动位置!', player);
        },

        // 视频播放
        onPlayerPlay(player) {
            console.log('Play>视频播放!', player);
        },

        // 视频播放中
        onPlayerPlaying(player) {
            console.log('Playing>视频播放中!', player);
        },

        // 视频时间更新中
        onPlayerTimeupdate(player) {
            console.log('Timeupdate>视频时间更新中!', player);
        },

        // 视频暂停
        onPlayerPause(player) {
            console.log('Pause>视频暂停!', player);
        },

        // 视频加载中
        onPlayerWaiting(player) {
            console.log('Waiting>视频加载中!', player);
        },

        // 视频状态改变
        playerStateChanged(playerCurrentState) {
            console.log('StateChanged>视频状态改变', playerCurrentState);
        },

        // 视频播放结束
        onPlayerEnded(player) {
            console.log('Ended>视频播放结束!', player);
        }

    // --------------------视频播放-结束-------------------- */
    }
};
</script>

<style lang="less" scoped>
</style>

