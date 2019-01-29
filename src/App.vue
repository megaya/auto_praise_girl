<template>
  <div id="app">
    <ul id="character-container">
    </ul>
    <video id="camera" autoplay></video>
    <button @click="initCamera">Open camera</button>

    <button type="button" class="btn btn-social btn-tw" @click="start">
      aa
    </button>
  </div>
</template>

<script>
import $ from 'jquery'

export default {
  name: 'App',
  data: function() {
    return {
      characterCount: 0,
      currentCharacters: [],
      voices: [
        { value: "がんばりましょう！", path: require('./assets/o.mp3') }
      ],
    }
  },
  methods: {
    initCamera: async function(e) {

      const constraints = window.constraints = { audio: false, video: true };

      try {
        const stream = await navigator.mediaDevices.getUserMedia(constraints);
        const video = document.querySelector('#camera');
        const videoTracks = stream.getVideoTracks();
        window.stream = stream; // make variable available to browser console
        video.srcObject = stream;
        e.target.disabled = true;
      } catch (e) {
        alert('カメラの使用を許可してください');
        console.log(e);
      }

    },
    sleep: function(ms) {
      return new Promise(resolve => setTimeout(resolve, ms));
    },
    createCharacter: function(text) {
      var ele = document.createElement('div');

      ele.innerHTML        = text;
      ele.style.position   = 'fixed';
      ele.style.left       = $(window).width() + 10 + 'px'; // 位置を左にする
      ele.style.width      = '500px';
      ele.style.top        = Math.floor( Math.random() * $(window).height()) + 'px'; // ランダムに高さを取得
      ele.setAttribute("id", "character-" + this.characterCount);
      ele.style.fontSize   = '40px';
      //ele.style.textShadow = '0 0 5px #111'
      //ele.style.color      = color;
      $('#character-container').append(ele);
    },
    playVoice: function() {
    },
    start: function() {
      this.characterCount = this.characterCount + 1;

      var videosIndex = Math.floor(Math.random() * this.voices.length);
      var voice = this.voices[videosIndex];

      this.createCharacter(voice.value);

      var v = new Audio(voice.path);
      v.play();

      $('#character-' + this.characterCount).show().animate({left: -500}, { duration: 3000, easing: 'linear',
        complete: function() {
          $(this).remove();
        }
      });
    }
  }
}
</script>

<style>
#camera {
  width: 100%;
}
</style>
