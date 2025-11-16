
<template>
  <div class="flex flex-col justify-center items-center border-b-4 p-4 gap-4">
    <h1 class="text-4xl font-bold">Radix Sort Visualiser</h1>
    <div class="flex flex-row gap-5 justify-center items-center">
      <button class="border-2 px-4 py-2 cursor-pointer" @click="radixSortReal(array, delay)">Radix Sort Real Time</button>
      <button class="border-2 px-4 py-2 cursor-pointer" @click="radixSortVisual(array, delay)">Radix Sort</button>
      <button class="border-2 px-4 py-2 cursor-pointer" @click="shuffle(size)">Shuffle</button>
      <input type="range" min="10" max="500" value="10" v-model="size">
      <label for="size">Size : {{ size }}</label>
      <input type="range" min="10" max="500" value="50" v-model="delay">
      <label for="speed">Delay : {{ delay }}</label>
    </div>
  </div>
  
  <div class="flex justify-center items-center p-10">
    <canvas ref="canvas" class="block" height="500" width="900"></canvas>
  </div>



  
</template>

<script setup>
import { ref,  onMounted } from 'vue';


const canvas = ref(null);
const size = ref(10);
const delay = ref(10);
const isSorting = ref(false);
let ctx;
let array = [];

onMounted(() => {
  ctx = canvas.value.getContext('2d');
  ctx.fillStyle = 'black';
  array = [1,2,3,4,5,6,7,8,9,10]
  drawBars(array);
})

function drawBars(arr) {
  ctx.clearRect(0, 0, canvas.value.width, canvas.value.height)

  const barWidth = canvas.value.width / arr.length

  arr.forEach((value, index) => {
    ctx.fillStyle = "cyan"
    const barHeight = value/size.value * canvas.value.height
    const x = index * barWidth
    const y = canvas.value.height - barHeight

    ctx.fillRect(x, y, barWidth, barHeight)
  })
}

function drawBarsHighlight(arr, highlightIndices = []) {
  ctx.clearRect(0, 0, canvas.value.width, canvas.value.height)

  const barWidth = canvas.value.width / arr.length

  arr.forEach((value, index) => {
    if (highlightIndices.includes(index)) {
      ctx.fillStyle = "red"
    } else {
      ctx.fillStyle = "cyan"
    }
    const barHeight = value/size.value * canvas.value.height
    const x = index * barWidth
    const y = canvas.value.height - barHeight

    ctx.fillRect(x, y, barWidth, barHeight)
  })
}

function getRandomInt(min, max) {
  return Math.floor(Math.random() * (max - min + 1)) + min;
}

function shuffle(num){
  array = [];
  for(let i=0; i<num; i++){
    let j = getRandomInt(1, num)
    while(array.includes(j)){
      j = getRandomInt(1, num)
    }
    array[i] = j
  }
  drawBars(array);
  console.log(array);
}

function sleep(ms) {
  return new Promise(resolve => setTimeout(resolve, ms));
}


async function countingSortForRadixVisual(array, exp, speed) {
  const n = array.length;
  const output = new Array(n).fill(0);
  const count = new Array(10).fill(0);

  for (let i = 0; i < n; i++) {
    const digit = Math.floor(array[i] / exp) % 10;
    count[digit]++;
  }

  for (let i = 1; i < 10; i++) count[i] += count[i - 1];

  for (let i = n - 1; i >= 0; i--) {
    const digit = Math.floor(array[i] / exp) % 10;
    output[count[digit] - 1] = array[i];
    count[digit]--;
  }

  for (let i = 0; i < n; i++) {
    array[i] = output[i];
    drawBarsHighlight(array, [i]);
    await sleep(speed);
  }
}

async function countingSortForRadixReal(array, exp, speed) {
  const output = new Array(array.length).fill(0);
  const count = new Array(10).fill(0);

  for (let i = 0; i < array.length; i++) {
    const digit = Math.floor(array[i] / exp) % 10;
    count[digit]++;
  }

  for (let i = 1; i < 10; i++) count[i] += count[i - 1];

  for (let i = array.length - 1; i >= 0; i--) {
    const digit = Math.floor(array[i] / exp) % 10;
    const idx = count[digit] - 1;
    output[idx] = array[i];
    count[digit]--;

    drawBars(output, [idx]); 
    await sleep(speed); 
  }

  for (let i = 0; i < array.length; i++) array[i] = output[i];
}

async function radixSortReal(array, speed) {


  if (isSorting.value) return;
  isSorting.value = true;

  const max = Math.max(...array);
  let exp = 1;
  while (Math.floor(max / exp) > 0) {
    await countingSortForRadixReal(array, exp, speed);
    exp *= 10;
  }
  isSorting.value = false;
}

async function radixSortVisual(array, speed) {


  if (isSorting.value) return;
  isSorting.value = true;

  const max = Math.max(...array);
  let exp = 1;
  while (Math.floor(max / exp) > 0) {
    await countingSortForRadixVisual(array, exp, speed);
    exp *= 10;
  }
  isSorting.value = false;
}


</script>

<style scoped>

</style>
