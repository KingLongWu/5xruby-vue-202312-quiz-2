<script setup>
import { ref, watch } from 'vue';
import CardItem from './components/Card.vue'

// 試完成以下功能：
//  1. 點擊卡片，卡片會翻開 (已完成)
//  2. 點擊兩張不同的卡片，卡片會翻回去 
//  3. 點擊兩張相同的卡片，卡片會消失
//  4. 當所有卡片都消失時，顯示「恭喜破關，再來一局？」的對話框，按下確定後重置遊戲
//  5. 將卡片獨立抽出為 Card.vue 元件

const cards = ref([]);
const openedCard = ref([]);
const matchCard = ref([]);
const hiddenCard = ref([]);

// 遊戲初始化，洗牌
const gameInit = () => {
  const numArr = [...new Array(16).keys()].map(i => ++i);
  numArr.sort(() => Math.random() - 0.5);
  cards.value = numArr.map(d => (d % 8) + 1);
  openedCard.value = [];
  hiddenCard.value = [];
}

const clickHandler = (idx, n) => {
  if (openedCard.value.length > 1) {
    return false;
  }

  openedCard.value.push(idx);
  matchCard.value.push(n);

  // 一秒後將 openedCard 清空 (牌面覆蓋回去)
  window.setTimeout(() => {

    if (openedCard.value.length === 2) {
      if (matchCard.value[0] === matchCard.value[1]) {
        if (hiddenCard.value.indexOf(n) === -1) {
          hiddenCard.value.push(n);
          if (hiddenCard.value.length === 8)
            if (confirm("恭喜破關，再來一局？"))
              gameInit();
        }
      }
    }
    openedCard.value = [];
    matchCard.value = [];
  }, 3000);
};

</script>

<template>
  <div class="bg-emerald-900 h-[100vh] w-full top-0 left-0 z-10 absolute">

    <div class="my-10 text-white text-center ">
      <div class="mb-8 text-5xl">五倍對對碰</div>
      <button @click="gameInit" class="rounded font-bold bg-blue-500 mx-6 text-white py-2 px-4 hover:bg-blue-700">{{
        cards.length === 16 ? "重置" : "開始" }}</button>
    </div>

    <div class="rounded-xl mx-auto border-4 mt-12 grid grid-flow-col p-10 w-[900px] gap-2 grid-rows-4">

      <CardItem
       :cards="cards" 
       :openedCard="openedCard"
       :hiddenCard="hiddenCard"
       @clickHandler="clickHandler"></CardItem>
      
    </div>
  </div>
</template>

<style scoped></style>