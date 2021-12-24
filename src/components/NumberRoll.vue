<script setup lang="ts">
interface WrapperRotateFlag {
  index: number;
  rotate: number;
}
interface NumbersConfig {
  label: number;
  rotateX: number;
}
import { computed, reactive, ref, watchEffect } from "vue";

const props = defineProps<{ totalValue: number }>();

const wrapperRotateFlag = reactive<WrapperRotateFlag[]>([]);
for (let i = 0; i < String(props.totalValue).length; i++) {
  wrapperRotateFlag.push({
    index: Number(String(props.totalValue)[i]),
    rotate: 0,
  });
}
const numbersConfig = computed(() => {
  let numbersCfg: NumbersConfig[][] = [];
  String(props.totalValue)
    .split("")
    .forEach((item) => {
      let itemConfig = (() => {
        let arr = [];
        for (let i = 0; i < 10; i++) {
          arr.push({
            label: i,
            rotateX: (360 / 10) * i,
          });
        }
        return arr;
      })();
      numbersCfg.push(itemConfig);
    });
  return numbersCfg;
});
watchEffect(() => {
  // 增减位判断保护
  if (String(props.totalValue).length > wrapperRotateFlag.length) {
    for (
      let i = wrapperRotateFlag.length;
      i < String(props.totalValue).length;
      i++
    ) {
      const idx = wrapperRotateFlag.findIndex(
        (m) => m.index === Number(String(props.totalValue)[i])
      );
      if (idx >= 0) {
        wrapperRotateFlag[idx] = {
          index: Number(String(props.totalValue)[i]),
          rotate: 0,
        };
      } else {
        wrapperRotateFlag.push({
          index: Number(String(props.totalValue)[i]),
          rotate: 0,
        });
      }
    }
  }
});
const rotateFlagUpdate = (itemstr: string, index: number) => {
  const item = Number(itemstr);
  let lastIndex = wrapperRotateFlag[index].index;
  let lastRotate = wrapperRotateFlag[index].rotate;
  let targetRotate;
  let abs;
  let direct;
  // 在一个循环中依据先后两数的大小，判断圆环旋转方向
  if (item >= lastIndex) {
    if (Math.abs(item - lastIndex) === Math.abs(lastIndex - item + 10)) {
      direct = true;
      abs = Math.abs(item - lastIndex);
    } else if (Math.abs(item - lastIndex) < Math.abs(lastIndex - item + 10)) {
      direct = true;
      abs = Math.abs(item - lastIndex);
    } else {
      direct = false;
      abs = Math.abs(lastIndex - item + 10);
    }
  } else {
    if (Math.abs(item - lastIndex) === Math.abs(lastIndex - item + 10)) {
      direct = true;
      abs = Math.abs(item - lastIndex);
    } else if (Math.abs(lastIndex - item) < Math.abs(item + 10 - lastIndex)) {
      direct = false;
      abs = Math.abs(lastIndex - item);
    } else {
      direct = true;
      abs = Math.abs(item + 10 - lastIndex);
    }
  }
  if (lastIndex === item) {
    if (lastRotate === 0) {
      targetRotate = item * -36;
    } else {
      targetRotate = lastRotate;
    }
  } else if (direct) {
    // 加
    targetRotate = lastRotate - abs * 36;
  } else if (!direct) {
    // 减
    targetRotate = lastRotate + abs * 36;
  }
  wrapperRotateFlag[index].index = item;
  wrapperRotateFlag[index].rotate = targetRotate ?? 0;
  return targetRotate;
};
</script>

<template>
  <div class="input-wrapper">
    <div
      class="stage"
      v-for="(item, index) in String(totalValue).split('')"
      :key="index"
    >
      <div
        class="wrapper"
        :style="{
          transform: `rotateX(${rotateFlagUpdate(item, index)}deg)`,
        }"
      >
        <div
          class="number-wrapper"
          v-for="(t, index) in numbersConfig[index]"
          :key="index"
          :style="{ transform: `rotateX(${t.rotateX}deg) translateZ(40px)` }"
        >
          {{ t.label }}
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.input-wrapper {
  display: flex;
  width: 200px;
  height: 40px;
  position: relative;
}
.stage {
  z-index: 1;
  width: 20px;
  height: 40px;
  overflow: hidden;
  perspective: 300px;
  position: relative;
}
.wrapper {
  position: absolute;
  left: 50%;
  transform-style: preserve-3d;
  height: 40px;
  /* transform: ${props => `rotateX(${props.x}deg)`}; */
  transition: opacity 1s, transform 1s;
}
.number-wrapper {
  position: absolute;
  backface-visibility: hidden;
  text-align: center;
  height: 40px;
  line-height: 40px;
  /* transform: ${props => `rotateX(${props.x}deg)`} translateZ(40px); */
}
</style>
