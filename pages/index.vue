<script setup lang="ts">
import { useSpeechSynthesis } from "@vueuse/core";

const voice = ref<SpeechSynthesisVoice>(
  undefined as unknown as SpeechSynthesisVoice
);
const text = ref("Hello, everyone! Good morning!");
const pitch = ref(1);
const rate = ref(1);

const speech = useSpeechSynthesis(text, {
  voice,
  pitch,
  rate,
});

let synth: SpeechSynthesis;

const voices = ref<SpeechSynthesisVoice[]>([]);

onMounted(() => {
  if (speech.isSupported.value) {
    // load at last
    setTimeout(() => {
      synth = window.speechSynthesis;
      voices.value = synth.getVoices();
      voice.value = voices.value[0];
    });
  }
});

function play() {
  if (speech.status.value === "pause") {
    console.log("resume");
    window.speechSynthesis.resume();
  } else {
    speech.speak();
  }
}

function pause() {
  window.speechSynthesis.pause();
}

function stop() {
  speech.stop();
}
</script>

<template>
  <div
    class="container mx-auto flex flex-col gap-8 pt-20 justify-center items-center"
  >
    <h1 class="text-4xl text-red">Speech Synthesis with NUXT 3</h1>
    <div v-if="!speech.isSupported">
      Your browser does not support SpeechSynthesis API,
      <a href="https://caniuse.com/mdn-api_speechsynthesis" target="_blank"
        >more details</a
      >
    </div>
    <div v-else>
      <!-- <input v-model="text" class="!inline-block" type="textarea" /> -->
      <textarea
        v-model="text"
        name="tts"
        id=""
        cols="80"
        rows="20"
        class="bg-slate-100 rounded-lg border border-slate-400 p-4"
      ></textarea>

      <div class="flex gap-8 py-10 justify-center items-center">
        <div>
          <label class="font-bold mr-2">Language</label>
          <div class="inline-flex items-center relative rounded bg-slate-100">
            <select
              v-model="voice"
              class="px-5 border-0 bg-transparent h-9 rounded"
            >
              <option disabled>Select Language</option>
              <option v-for="(voice, i) in voices" :key="i" :value="voice">
                {{ `${voice.name} (${voice.lang})` }}
              </option>
            </select>
          </div>
        </div>

        <div class="inline-flex items-center">
          <label class="font-bold mr-2">Pitch - {{ pitch }}</label>
          <div class="mt-1 inline-flex">
            <input v-model="pitch" type="range" min="0.5" max="2" step="0.1" />
          </div>
        </div>

        <div class="inline-flex items-center">
          <label class="font-bold mr-3">Rate - {{ rate }}</label>
          <div class="mt-1 inline-flex">
            <input v-model="rate" type="range" min="0.5" max="2" step="0.1" />
          </div>
        </div>
      </div>

      <div class="py-4 flex gap-5 text-xl text-blue-900">
        <button
          :disabled="speech.isPlaying.value"
          @click="play"
          class="rounded-lg py-4 px-5 bg-green-100 border border-green-100 hover:border-green-400"
        >
          {{ speech.status.value === "pause" ? "Resume" : "Speak" }}
        </button>
        <button
          :disabled="!speech.isPlaying.value"
          class="rounded-lg py-4 px-5 bg-orange-100 border border-orange-100 hover:border-orange-400"
          @click="pause"
        >
          Pause
        </button>
        <button
          :disabled="!speech.isPlaying.value"
          class="rounded-lg py-4 px-5 bg-red-100 border border-red-100 hover:border-red-400"
          @click="stop"
        >
          Stop
        </button>
      </div>
    </div>
  </div>
</template>
