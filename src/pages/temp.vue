<template>
    <div>
      <el-button>123</el-button>
      <input type="file" @change="handleFiles" accept="audio/*, .lrc"multiple>
      <audio :src="currentMusicUrl" ref="audioPlayer" @timeupdate="updateLyrics" @play="isPlaying = true" @pause="isPlaying = false"></audio>
      <button @click="togglePlay">{{ isPlaying ? 'Pause' : 'Play' }}</button>
      <div class="lyrics">
        <p v-for="(line, index) in lyrics" :key="index" :class="{ active: index === activeLyricIndex }">{{ line.text }}</p>
      </div>
    </div>
  </template>
  
  <script setup>
  import { ref, onMounted } from 'vue';
  
  const audioPlayer = ref(null);
  const isPlaying = ref(false);
  const currentMusicUrl = ref('');
  const lyrics = ref([]);
  const activeLyricIndex = ref(0);
  
  const parseLrc = (lrcContent) => {
    const lines = lrcContent.split('\n');
    const lyricsArray = lines.map(line => {
      const match = line.match(/\[(\d+:\d+\.\d+)\](.*)/);
      if (match) {
        const time = match[1];
        const text = match[2];
        const minutes = parseInt(time.split(':')[0], 10);
        const seconds = parseFloat(time.split(':')[1]);
        return {
          time: minutes * 60 + seconds,
          text: text.trim()
        };
      }
      return null;
    }).filter(l => l);
    return lyricsArray;
  };
  
  const handleFiles = async (event) => {
    const files = event.target.files;
    console.log("fules", files);
    if (files.length) {
      const musicFile = files[0];
      currentMusicUrl.value = URL.createObjectURL(musicFile);
  
      // Assuming the .lrc file has the same name as the music file
      const lrcFileName = musicFile.name.replace(/\.[^/.]+$/, '') + '.lrc';
      const lrcFile = Array.from(files).find(file => file.name === lrcFileName);
      if (lrcFile) {
        lrcFile.text().then(lrcContent => {
          lyrics.value = parseLrc(lrcContent);
        });
      }
    }
  };
  
  const togglePlay = () => {
    if (audioPlayer.value.paused) {
      audioPlayer.value.play();
    } else {
      audioPlayer.value.pause();
    }
  };
  
  const updateLyrics = () => {
    const currentTime = audioPlayer.value.currentTime;
    const newActiveLyricIndex = lyrics.value.findIndex((lyric, index) => {
      return index === lyrics.value.length - 1 || currentTime < lyrics.value[index + 1].time;
    });
    activeLyricIndex.value = newActiveLyricIndex;
  };
  </script>
  
  <style>
  .lyrics .active {
    color: red;
    font-weight: bold;
  }
  </style>
  