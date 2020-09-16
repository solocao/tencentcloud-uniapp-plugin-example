<template>
  <div class="wrapper">
    <button type="button" @click="startRecord">开始录音</button>
    <button @click="endRecord">停止录音</button>
    <button @click="playVoice">播放录音</button>
  </div>
</template>
<script>
import { sentenceRecognition } from "@/js_sdk/tencentcloud-plugin-asr";
import { MediaStream } from "@/js_sdk/tencentcloud-plugin-asr/webrtc";
export default {
  data() {
    return {
      data: "",
      rec: "",
    };
  },
  methods: {
    onLoad: function () {
    },
    async startRecord() {
      const stream = await MediaStream.getStream();
      stream.startRecording();
    },
    async endRecord() {
      const stream = await MediaStream.getStream();
      const blob = stream.stopRecording();

      var reader = new FileReader();
      reader.onloadend = async function () {
        
      };
      reader.readAsDataURL(blob);
    },
    playVoice() {
    },
    base64ToUint8Array(base64String) {
      let padding = "=".repeat((4 - (base64String.length % 4)) % 4);
      let base64 = (base64String + padding)
        .replace(/\-/g, "+")
        .replace(/_/g, "/");

      let rawData = window.atob(base64);
      let outputArray = new Uint8Array(rawData.length);

      for (var i = 0; i < rawData.length; ++i) {
        outputArray[i] = rawData.charCodeAt(i);
      }
      return outputArray;
    },
  },
};
</script>

<style>
.wrapper {
  margin: 40rpx;
}

.wrapper button {
  margin-top: 20rpx;
}
</style>
