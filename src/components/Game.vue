<script setup>
import { ref, watch } from "vue";

const colors = ["red", "blue", "green", "green", "yellow", "blue", "red"];
const lastResponseColor = ref("");
const lastResponseIndex = ref(-1);

const active = ref(null);
const index = ref(-1);
const startGame = ref(false);
const canPlay = ref(false);
const gameLost = ref(false);
const gameWin = ref(false);

const tellColor = (color) => {
  if ("speechSynthesis" in window) {
    let synthesis = window.speechSynthesis;

    // Get the first en language voice in the list
    let voice = synthesis.getVoices().filter(function (voice) {
      return voice.lang === "en";
    })[0];

    // Create an utterance object
    let utterance = new SpeechSynthesisUtterance(color);

    // Set utterance properties
    utterance.voice = voice;
    utterance.pitch = 1.5;
    utterance.rate = 1.25;
    utterance.volume = 2;

    // Speak the utterance
    synthesis.speak(utterance);
  } else {
    console.log("Text-to-speech not supported.");
  }
};

const sendNotification = () => {
  Notification.requestPermission().then((result) => {
    if (result === "granted") {
      const notifTitle = "Simon game";
      const notifBody = `Suuuu`;
      const options = {
        body: notifBody,
      };
      new Notification(notifTitle, options);
    }
  });
};

sendNotification();

const runGame = () => {
  index.value = 0;
};

const stopGame = () => {
  active.value = null;
  index.value = -1;
  canPlay.value = true;
};

watch(startGame, () => {
  if (startGame.value) {
    gameLost.value = false;
    gameWin.value = false;
    lastResponseColor.value = "";
    lastResponseIndex.value = -1;
    runGame();
  }
});

const nextColor = () => {
  setTimeout(() => {
    active.value = null;
  }, 900);
  setTimeout(() => {
    index.value++;
  }, 1000);
};

watch(index, () => {
  if (index.value >= 0 && index.value < colors.length) {
    tellColor(colors[index.value]);
    setTimeout(() => {
      active.value = colors[index.value];
      nextColor();
    }, 700);
  } else if (index.value === colors.length) {
    stopGame();
  }
});

const setResponse = (color) => {
  if (canPlay.value) {
    lastResponseColor.value = color;
    lastResponseIndex.value++;
  }
};

watch(lastResponseColor, () => {
  if (lastResponseColor.value === colors[lastResponseIndex.value]) {
    if (lastResponseIndex.value === colors.length - 1) {
      gameWin.value = true;
      startGame.value = false;
      canPlay.value = false;
    }
    tellColor(colors[lastResponseIndex.value]);
  } else {
    gameLost.value = true;
    startGame.value = false;
    canPlay.value = false;
  }
});
</script>

<template>
  <div class="h-full flex flex-col justify-center items-center">
    <div class="flex flex-wrap justify-center items-center">
      <div class="m-10">
        <button
          class="rounded p-4 bg-violet-600 text-white"
          @click="startGame = true"
        >
          Launch game
        </button>
      </div>
      <div class="w-80 h-80 grid grid-cols-2 gap-2">
        <div
          class="bg-green-500 rounded-tl-full"
          :class="
            (active === 'green' && 'brightness-200') ||
            (canPlay && 'cursor-pointer')
          "
          @click="setResponse('green')"
        ></div>
        <div
          class="bg-red-600 rounded-tr-full"
          :class="
            (active === 'red' && 'brightness-200') ||
            (canPlay && 'cursor-pointer')
          "
          @click="setResponse('red')"
        ></div>
        <div
          class="bg-yellow-400 rounded-bl-full"
          :class="
            (active === 'yellow' && 'brightness-200') ||
            (canPlay && 'cursor-pointer')
          "
          @click="setResponse('yellow')"
        ></div>
        <div
          class="bg-blue-600 rounded-br-full"
          :class="
            (active === 'blue' && 'brightness-200') ||
            (canPlay && 'cursor-pointer')
          "
          @click="setResponse('blue')"
        ></div>
      </div>
      <div v-if="startGame && !gameLost && !gameWin" class="m-10 font-bold">
        <span :class="canPlay ? 'text-green-500' : ''">{{
          canPlay ? "Répétez la séquence" : "Retenez la séquence"
        }}</span>
      </div>
      <div v-if="gameLost" class="m-10 font-bold">
        <span class="text-red-600 font-bold">Vous avez perdu !</span>
      </div>
      <div v-if="gameWin" class="m-10 font-bold">
        <span class="text-green-500 font-bold">Vous avez gagné !</span>
      </div>
    </div>
  </div>
</template>
