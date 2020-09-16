<template>
  <view class="wrapper">
    <view class="content">
      <view class="input">
        <view>视频：</view>
        <input :value="video && video.name" type="text" placeholder="点击选择" disabled @click="chooseVideo" />
      </view>
      <view class="input">
        <view>封面：</view>
        <input :value="image && image.name" type="text" placeholder="点击选择(可选)" disabled @click="chooseImage" />
      </view>
      <button @click="upload">上传视频</button>
      <view v-if="result" class="result">
        上传成功，视频ID是：
        <view>{{ result }}</view>
      </view>
      <view class="input">&nbsp;</view>
      <view class="input">&nbsp;</view>
      <view class="input">&nbsp;</view>
      <view class="input">&nbsp;</view>
      <view class="input">
        <view>视频ID：</view>
        <input id="mediaId" :value="mediaId" type="text" placeholder="请在腾讯云控制台查看" @input="setValue" />
      </view>
      <button @click="load">加载视频</button>
<!--      <view v-if="mediaInfo" class="result">
        <video :src="mediaInfo.AntiTheftUrl || mediaInfo.MediaUrl" :poster="mediaInfo.CoverUrl" />
      </view> -->
      <video src="http://1301800460.vod2.myqcloud.com/e3422259vodcq1301800460/8222efb75285890806972086861/YEcHfrVYbFMA.mov" />
    </view>
  </view>
</template>

<script>
  // #ifdef H5
  import TcVod from 'vod-js-sdk-v6';
  // #endif
  import getSignature from '@/js_sdk/tencentcloud-plugin-vod/get-upload-signature.js';
  import chooseFile from '@/js_sdk/tencentcloud-plugin-cos/choose-file.js';
  import getMediaInfo from '@/js_sdk/tencentcloud-plugin-vod/get-media-info.js'

  export default {
    data() {
      return {
        video: null,
        image: null,
        result: null,
        mediaId: '',
        mediaInfo: null,
      };
    },
    methods: {
      async chooseVideo() {
        const [file] = await chooseFile('video/*');
        this.video = file;
      },
      async chooseImage() {
        const [file] = await chooseFile('image/*');
        this.image = file;
      },
      setValue(e) {
        const { target: { id, value } } = e;
        this[id] = value;
      },
      // 上传视频
      async upload() {
        this.result = null;
        if (!this.video) {
          uni.showToast({
            icon: 'none',
            title: '请选择视频文件',
          });
          return;
        }
        const tcVod = new TcVod({ getSignature });
        const uploader = tcVod.upload({
          mediaFile: this.video,
          coverFile: this.image,
        });
        uni.showLoading({
          mask: true,
        });
        try {
          const result = await uploader.done();
          console.log('resultdone', result)
          this.result = result.fileId;
          uni.hideLoading();
        } catch (error) {
          uni.showToast({
            icon: 'none',
            title: error.message,
          });
        }
      },
      // 加载视频信息
      async load() {
        if (!this.mediaId) {
          uni.showToast({
            icon: 'none',
            title: '请输入视频ID',
          });
          return;
        }
        this.mediaInfo = null;
        uni.showLoading({
          mask: true,
        });
        try {
          const result = await getMediaInfo(this.mediaId);
          console.log('resulturl', result)
          if (result) {
            this.mediaInfo = result.BasicInfo;
            uni.hideLoading();
          } else {
            uni.showToast({
              icon: 'none',
              title: '无效的视频ID',
            });
          }
        } catch (error) {
          uni.showToast({
            icon: 'none',
            title: error.message,
          });
        }
      },
    }
  };
</script>

<style>
  .content {
    margin: 40rpx;
  }

  .content button {
    margin: 20rpx 0
  }

  .input {
    display: flex;
    margin-top: 20rpx;
  }

  .input view {
    width: 4rem;
    text-align: right;
    font-size: .8rem;
  }

  .input input {
    flex: 1 0 0;
    border: 1px solid #999;
    border-radius: 8rpx;
    padding: 4rpx 12rpx;
  }

  .result {
    margin-top: 20rpx;
    padding: 10rpx;
    border: 1px dashed #81b8ff;
    border-radius: 8rpx;
    font-size: 0.75rem;
    word-break: break-all;
    user-select: text;
  }

  .result video {
    width: 100%;
  }
</style>
