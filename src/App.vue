<script setup lang="ts">
// This starter template is using Vue 3 <script setup> SFCs
// Check out https://v3.vuejs.org/api/sfc-script-setup.html#sfc-script-setup
import { ref } from "vue";
import NumberRoll from "./components/NumberRoll.vue";
const num = ref(99);
const add = () => {
  num.value++;
};
const sub = () => {
  num.value--;
};
</script>

<template>
  <div>
    <input type="text" v-model="num" />
    <button @click="add()">+1</button>
    <button @click="sub()">-1</button>
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
