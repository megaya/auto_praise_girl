<template>
  <div id="app">
    <ul id="character-container">
    </ul>

    <button type="button" class="btn btn-social btn-tw" :disabled="!isActive" @click="stop">ストップ</button>
    <button type="button" class="btn btn-social btn-tw" :disabled="isActive" @click="animateCharacter()">1回だけ応援してもらう</button>
    <button type="button" class="btn btn-social btn-tw" :disabled="isActive" @click="start(3000, 1000)">少しだけ応援してもらう</button>
    <button type="button" class="btn btn-social btn-tw" :disabled="isActive" @click="start(2000, 1000)">そこそこ応援してもらう</button>
    <button type="button" class="btn btn-social btn-tw" :disabled="isActive" @click="start(1000, 500)">心地いいペースで応援してもらう</button>
    <button type="button" class="btn btn-social btn-tw" :disabled="isActive" @click="start(500, 100)">ものすごく応援してもらう</button>
    <button type="button" class="btn btn-social btn-tw" :disabled="isActive" @click="start(100, 10)">過剰に応援してもらう</button>
    <button type="button" class="btn btn-social btn-tw" :disabled="isActive" @click="start(10, 1)">応援地獄 (※ 音量注意)</button>

    <button @click="initCamera">カメラを起動する</button>
    <video id="camera" autoplay></video>
  </div>
</template>

<script>
import $ from 'jquery'

export default {
  name: 'App',
  data: function() {
    return {
      isActive: false,
      characterCount: 0,
      currentCharacters: [],
      voices: [
        // voice_girl
        // { value: "がんばりましょう！",    path: require('./assets/soundeffect_lab/voice_girl/gannbarimasho.mp3') },
        { value: "おめでとうございます",  path: require('./assets/soundeffect_lab/voice_girl/omedetougozaimasu.mp3') },
        { value: "すごい！すごい！",      path: require('./assets/soundeffect_lab/voice_girl/sugoisugoi.mp3') },
        { value: "合格です",              path: require('./assets/soundeffect_lab/voice_girl/goukaku.mp3') },
        { value: "よくできました",        path: require('./assets/soundeffect_lab/voice_girl/yokudekimasita.mp3') },
        { value: "グッド!!",              path: require('./assets/soundeffect_lab/voice_girl/good.mp3') },
        { value: "レベルアップしました",  path: require('./assets/soundeffect_lab/voice_girl/levelup.mp3') },
        // info_girl
        { value: "よくできました",        path: require('./assets/soundeffect_lab/info_girl/yokudekimashita.mp3') },
        { value: "おめでとうございます",  path: require('./assets/soundeffect_lab/info_girl/omedetou.mp3') },
        // { value: "がんばりましょう",      path: require('./assets/soundeffect_lab/info_girl/ganbarimasyou1.mp3') },
        { value: "満点",                  path: require('./assets/soundeffect_lab/info_girl/manten1.mp3') },
        { value: "レベルアップしました",  path: require('./assets/soundeffect_lab/info_girl/levelupshimashita1.mp3') },
        // etc
        { value: "88888888888888888888",  path: require('./assets/soundeffect_lab/etc/people_people-stadium-applause1.mp3') },
        // maya
        { value: "大好き",                  path: require('./assets/maya/voice-daisuki.wav') },
        { value: "すごい",                  path: require('./assets/maya/voice-sugoi.wav') },
        { value: "さすが！",                path: require('./assets/maya/voice-sasuga.wav') },
        { value: "尊敬します",              path: require('./assets/maya/voice-sonkei-shimasu.wav') },
        { value: "おめでとうございます",    path: require('./assets/maya/voice-omedetougozaimasu-akarume.wav') },
        // { value: "がんばって！",            path: require('./assets/maya/voice-ganbatte-akarume.wav') },
        { value: "いいね！",                path: require('./assets/maya/voice-waratte-akarume.wav') },
        // soalunashosya
        { value: "ありがとう",                path: require('./assets/soalunashosya/shozyo2-arigato.mp3') },
        { value: "よく頑張りました！よしよし",path: require('./assets/soalunashosya/shozyo2-yokuganbarimaita.mp3') },
        { value: "やりました",                path: require('./assets/soalunashosya/shozyo1-yarimasita.mp3') },
        { value: "ありがとう",                path: require('./assets/soalunashosya/shozyo1-arigatou.mp3') },
        { value: "ありがとう",                path: require('./assets/soalunashosya/syozyo3-arigato.mp3') },
        { value: "OK",                        path: require('./assets/soalunashosya/syozyo3-ok.mp3') },
        { value: "勝利じゃな",                path: require('./assets/soalunashosya/shozyo4-shorizyana.mp3') },
        { value: "うむ、その意気じゃ！",      path: require('./assets/soalunashosya/shozyo4-sonoikizya.mp3') },
        { value: "皆、よくやったのう",        path: require('./assets/soalunashosya/shozyo4-minayokuyattano.mp3') },
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
    start: async function(max, min) {
      this.isActive = true;

      while(this.isActive){
        this.animateCharacter();

        var sleepTime = Math.floor( Math.random() * (max + 1 - min) ) + min ;
        await this.sleep(sleepTime);
      }
    },
    stop: function() {
      this.isActive = false;
    },
    createCharacter: function(text) {
      var ele = document.createElement('div');
      ele.innerHTML        = text;
      ele.style.position   = 'fixed';
      ele.style.left       = $(window).width() + 10 + 'px'; // 位置を左にする
      ele.style.width      = '700px';
      ele.style.top        = Math.floor( Math.random() * $(window).height()) + 'px'; // ランダムに高さを取得
      ele.setAttribute("id", "character-" + this.characterCount);
      ele.style.fontSize   = '40px';
      //ele.style.textShadow = '0 0 5px #111'
      //ele.style.color      = color;
      return ele;
    },
    randomVoice: function() {
      var videosIndex = Math.floor(Math.random() * this.voices.length);
      var voice = this.voices[videosIndex];
      return voice;
    },
    playVoice: function(voice) {
      var v = new Audio(voice.path);
      v.play();
    },
    animateCharacter: function() {
      this.characterCount = this.characterCount + 1;

      let voice = this.randomVoice();
      let elment = this.createCharacter(voice.value);

      $('#character-container').append(elment);
      this.playVoice(voice);

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
