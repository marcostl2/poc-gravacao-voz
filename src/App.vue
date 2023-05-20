<template>
  <div>
    <audio ref="audioRef" controls></audio>
    <button @click="startRecording">Start Recording</button>
    <button @click="stopRecording">Stop Recording</button>
  </div>
</template>

<script lang="ts" setup>
import { ref } from 'vue';

const audioRef = ref<HTMLAudioElement | null>(null);
const mediaRecorder = ref<MediaRecorder | null>(null);
const chunks = ref<Blob[]>([]);

const startRecording = () => {
  navigator.mediaDevices.getUserMedia({ audio: true }).then((stream) => {
    mediaRecorder.value = new MediaRecorder(stream);
    mediaRecorder.value.ondataavailable = (e) => {
      chunks.value.push(e.data);
    };
    mediaRecorder.value.onstop = () => {
      const blob = new Blob(chunks.value, { type: 'audio/ogg; codecs=opus' });
      chunks.value = [];
      const audioURL = URL.createObjectURL(blob);
      audioRef.value!.src = audioURL;

      // sendAudio(blob);
    };
    mediaRecorder.value.start();
  });
};

const stopRecording = () => {
  mediaRecorder.value?.stop();
};

// const sendAudio = async (blob: Blob) => {
//   const formData = new FormData();
//   formData.append('audio', blob, 'audio.ogg');

//   const response = await fetch('https://your-api-url.com', {
//     method: 'POST',
//     body: formData,
//   });

//   if (!response.ok) {
//     throw new Error(`HTTP error! status: ${response.status}`);
//   }

//   const data = await response.json();
//   console.log(data);
// };
</script>
