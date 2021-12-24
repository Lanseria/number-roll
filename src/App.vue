<script setup lang="ts">
// This starter template is using Vue 3 <script setup> SFCs
// Check out https://v3.vuejs.org/api/sfc-script-setup.html#sfc-script-setup
import { ref, watch } from "vue";
import { useMousePressed } from "@vueuse/core";
import NumberRoll from "./components/NumberRoll.vue";
const num = ref(99);
const addRef = ref(null);
const subRef = ref(null);

const add = () => {
  num.value++;
};
const sub = () => {
  num.value--;
};

const { pressed: addPressed } = useMousePressed({ target: addRef });
const { pressed: subPressed } = useMousePressed({ target: subRef });
watch([addPressed, subPressed], ([a, s], [ap, sp]) => {
  let interval;
  if (a) {
    console.log("a true ?");
    interval = setInterval(() => {
      add();
    }, 100);
  }
  if (!a) {
    clearInterval(interval);
  }
  console.log(a, s);
});
</script>

<template>
  <div>
    <input type="text" v-model="num" />
    <button ref="addRef">+1</button>
    <button ref="subRef">-1</button>
  </div>
  <NumberRoll :total-value="num" />
</template>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
