<script setup>
import { ref, watch } from "vue";

const order = ["red", "blue", "green", "green", "yellow", "blue", "red"];

const active = ref(null);
const startGame = ref(false);

const runGame = () => {
  let i = 0;
  while (i < order.length) {
    (function (i) {
      setTimeout(function () {
        active.value = order[i];
      }, 1000 * i);
    })(i++);
  }
};

watch(startGame, () => {
  if (startGame.value) {
    runGame();
  }
});
</script>

<template>
  <div class="h-full flex justify-center items-center">
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
        :class="active === 'green' && 'brightness-200'"
      ></div>
      <div
        class="bg-red-600 rounded-tr-full"
        :class="active === 'red' && 'brightness-200'"
      ></div>
      <div
        class="bg-yellow-400 rounded-bl-full"
        :class="active === 'yellow' && 'brightness-200'"
      ></div>
      <div
        class="bg-blue-600 rounded-br-full"
        :class="active === 'blue' && 'brightness-200'"
      ></div>
    </div>
  </div>
</template>
