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
    active.value = colors[index.value];
    nextColor();
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
    console.log("yes");
  } else {
    gameLost.value = true;
    startGame.value = false;
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
      <div v-if="startGame && !gameLost" class="m-10 font-bold">
        <span :class="canPlay ? 'text-green-500' : ''">{{
          canPlay ? "Répétez la séquence" : "Retenez la séquence"
        }}</span>
      </div>
      <div v-if="gameLost" class="m-10 font-bold">
        <span class="text-red-600 font-bold">Vous avez perdu !</span>
      </div>
    </div>
  </div>
</template>
