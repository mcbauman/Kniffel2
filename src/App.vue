<script setup>
import { computed, ref } from 'vue';

const dice = ref(["-","-","-","-","-"])
const diceLock = ref([false, false, false, false, false])
const trowsCount = ref(0)
const results = ref([false, false, false, false, false])
const results2 = ref({equals:[false,false,false],chance:false})
const allDicePoints = computed(()=>dice.value[0]+dice.value[1]+dice.value[2]+dice.value[3]+dice.value[4])

function lockDice(index){
  diceLock.value[index]=!diceLock.value[index]
}

function throwDice(){
  for(let i = 0; i<5; i++){
    if(!diceLock.value[i]){
      dice.value[i]=Math.ceil(Math.random()*6)
    }
  }
  trowsCount.value++
}

function countCertainkind(kind){
  let sum=0
  dice.value.forEach(oneDice => {
    if(oneDice===kind){
      sum=sum+kind
    }
  });
  results.value[kind-1]=sum
  trowsCount.value=0
  diceLock.value=[false, false, false, false, false]
  dice.value=["-","-","-","-","-"]
}

function checkEquals(number){
  let equalDices=0
  for(let i = 1; i < 7 ; i++){
    let NumberCount=dice.value.filter(x=>x===i).length
    if(equalDices<NumberCount){
      equalDices=NumberCount
    }
  }
  if(equalDices>=number){
    if(number===5){
      results2.value.equals[number-3]=50
    }else{
      results2.value.equals[number-3]=allDicePoints.value
    }
  }else{  
    results2.value.equals[number-3]=0
  }
  trowsCount.value=0
  diceLock.value=[false, false, false, false, false]
  dice.value=["-","-","-","-","-"]
}

function checkSmallStreet(){
  if((dice.value.includes(1)&&dice.value.includes(2)&&dice.value.includes(3)&&dice.value.includes(4))||
    (dice.value.includes(2)&&dice.value.includes(3)&&dice.value.includes(4)&&dice.value.includes(5))||
    (dice.value.includes(3)&&dice.value.includes(4)&&dice.value.includes(5)&&dice.value.includes(6))){
      results2.value.smallStreet=30
    }else{
      results2.value.smallStreet=0
    }
  trowsCount.value=0
  diceLock.value=[false, false, false, false, false]
  dice.value=["-","-","-","-","-"]
}

function checkBigStreet(){
  if((dice.value.includes(1)&&dice.value.includes(2)&&dice.value.includes(3)&&dice.value.includes(4)&&dice.value.includes(5))||
    (dice.value.includes(2)&&dice.value.includes(3)&&dice.value.includes(4)&&dice.value.includes(5)&&dice.value.includes(6))){
      results2.value.bigStreet=40
    }else{
      results2.value.bigStreet=0
    }
  trowsCount.value=0
  diceLock.value=[false, false, false, false, false]
  dice.value=["-","-","-","-","-"]
}

function checkFullHouse(){
  let equalDices=[]
  for(let i = 1; i < 7 ; i++){
    let NumberCount=dice.value.filter(x=>x===i).length
    equalDices[i]=NumberCount
  }
  if((equalDices.includes(3) && equalDices.includes(2)) || equalDices.includes(5)){
    results2.value.fullHouse=25
  }else{
    results2.value.fullHouse=0
  }
  trowsCount.value=0
  diceLock.value=[false, false, false, false, false]
  dice.value=["-","-","-","-","-"]
}

function setChance(){
  results2.value.chance=allDicePoints.value
  trowsCount.value=0
  diceLock.value=[false, false, false, false, false]
  dice.value=["-","-","-","-","-"]
}
</script>

<template>
  <header>
    <h1>Kniffel</h1>
    {{ trowsCount }}
    <button v-if="trowsCount<3" @click.prevent="throwDice()">Würfeln</button>
  </header>
    <body>
      <article id="dices">
        <div class="oneDice" 
          v-for="(oneDice, index) in dice" 
          :class="diceLock[index]?'locked':''"
          @click.prevent="lockDice(index)">
          {{ oneDice }}
        </div>
    </article>
    <article id="points">
    {{ results }}
      <span v-if="results[0] || results[0]===0">{{ results[0] }}</span>
      <button v-else @click.prevent="countCertainkind(1)">1</button>
      <span v-if="results[1] || results[1]===0">{{ results[1] }}</span>
      <button v-else @click.prevent="countCertainkind(2)">2</button>
      <span v-if="results[2] || results[2]===0">{{ results[2] }}</span>
      <button v-else @click.prevent="countCertainkind(3)">3</button>
      <span v-if="results[3] || results[3]===0">{{ results[3] }}</span>
      <button v-else @click.prevent="countCertainkind(4)">4</button>
      <span v-if="results[4] || results[4]===0">{{ results[4] }}</span>
      <button v-else @click.prevent="countCertainkind(5)">5</button>
      <span v-if="results[5] || results[5]===0">{{ results[5] }}</span>
      <button v-else @click.prevent="countCertainkind(6)">6</button>
    </article>
    <article id="points2">
    {{results2}}
    <span v-if="results2.fullHouse || results2.fullHouse === 0">{{ results2.fullHouse }}</span>
    <button v-else @click.prevent="checkFullHouse()">FullHouse</button>
    <span v-if="results2.smallStreet || results2.smallStreet === 0">{{ results2.smallStreet }}</span>
    <button v-else @click.prevent="checkSmallStreet()">smallStreet</button>
    <span v-if="results2.bigStreet || results2.bigStreet === 0">{{ results2.bigStreet }}</span>
    <button v-else @click.prevent="checkBigStreet()">gibStreet</button>
    <span v-if="results2.equals[0] || results2.equals[0] === 0">{{ results2.equals[0] }}</span>
    <button v-else @click.prevent="checkEquals(3)">equals3</button>
    <span v-if="results2.equals[1] || results2.equals[1] === 0">{{ results2.equals[1] }}</span>
    <button v-else @click.prevent="checkEquals(4)">equals4</button>
    <span v-if="results2.equals[2] || results2.equals[2] === 0">{{ results2.equals[2] }}</span>
    <button v-else @click.prevent="checkEquals(5)">Kniffel</button>
    <span v-if="results2.chance || results2.chance === 0">{{ results2.chance }}</span>
    <button v-else @click.prevent="setChance()">Chance</button>
    </article>
  </body>
</template>

<style>
  body{
    background-image: url(./assets/pic.jpeg);
    background-size: cover;
    padding:0;
    margin: 0;
    font-family: 'Franklin Gothic Medium', 'Arial Narrow', Arial, sans-serif;
  }
  header{
    background-color: rgba(0, 0, 0, 0.7);
    color: white;
  }
  button{
    width: 100px;
    height: 50px;
    margin: 10px;
  }
  #dices{
    display: flex;
    justify-content: space-around;
  }
  .oneDice{
    background-color: wheat;
    color: black;
    width: 50px;
    height: 50px;
    font-size: xx-large;
    text-align: center;
  }
  span{
    background-color: wheat;
    color: black;
    width: 50px;
    height: 50px;
    font-size: xx-large;
    text-align: center;
    margin: 10px;
  }
  .locked{
    background-color: red;
  }
</style>