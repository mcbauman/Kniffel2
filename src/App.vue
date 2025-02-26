<script setup>
import { computed, ref } from 'vue';
import GenericInput from './components/GenericInput.vue';
import DiceIcon from './assets/Icons/DiceIcon.vue';
import ReloadIcon from './assets/Icons/ReloadIcon.vue';

const dice = ref(["-","-","-","-","-"])
const diceLock = ref([false, false, false, false, false])
const trowsCount = ref(0)
const results = ref([false, false, false, false, false,false])
const results2 = ref({equals:[false,false,false],chance:false})
const allDicePoints = computed(()=>dice.value[0]+dice.value[1]+dice.value[2]+dice.value[3]+dice.value[4])
const highscore = ref(JSON.parse(localStorage.getItem("highscore"))||0)
const score = ref(0)

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

function smallReset(){
  trowsCount.value=0
  diceLock.value=[false, false, false, false, false]
  dice.value=["-","-","-","-","-"]
  console.log("SmallReset");
  countScore()
  writeScore()
}

function countCertainkind(kind){
  let sum=0
  dice.value.forEach(oneDice => {
    if(oneDice===kind){
      sum=sum+kind
    }
  });
  results.value[kind-1]=sum
  smallReset()
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
  smallReset()
}

function checkSmallStreet(){
  if((dice.value.includes(1)&&dice.value.includes(2)&&dice.value.includes(3)&&dice.value.includes(4))||
    (dice.value.includes(2)&&dice.value.includes(3)&&dice.value.includes(4)&&dice.value.includes(5))||
    (dice.value.includes(3)&&dice.value.includes(4)&&dice.value.includes(5)&&dice.value.includes(6))){
      results2.value.smallStreet=30
    }else{
      results2.value.smallStreet=0
    }
    smallReset()
}

function checkBigStreet(){
  if((dice.value.includes(1)&&dice.value.includes(2)&&dice.value.includes(3)&&dice.value.includes(4)&&dice.value.includes(5))||
    (dice.value.includes(2)&&dice.value.includes(3)&&dice.value.includes(4)&&dice.value.includes(5)&&dice.value.includes(6))){
      results2.value.bigStreet=40
    }else{
      results2.value.bigStreet=0
    }
    smallReset()
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
  smallReset()
}

function setChance(){
  results2.value.chance=allDicePoints.value
  smallReset()
}

function newGame(){
  results.value = [false, false, false, false, false,false]
  results2.value = {equals:[false,false,false],chance:false}
  smallReset()
  highscore.value = ref(JSON.parse(localStorage.getItem("highscore"))||0)
  score.value=0
}

function countScore(){
  score.value=0
  results.value.forEach(element=>{
    if(element){
      score.value += element
    }
  })
  results2.value.equals.forEach(element=>{
    if(element){
      score.value += element
    }
  })
  if(results2.value.smallStreet){
    score.value += results2.value.smallStreet
  }
  if(results2.value.bigStreet){
    score.value += results2.value.bigStreet
  }
  if(results2.value.fullHouse){
    score.value += results2.value.fullHouse
  }
  if(results2.value.chance){
    score.value += results2.value.chance
  }
  console.log("countScoreExec",score.value);
  
}

function writeScore(){
  if(score.value>highscore.value){
    localStorage.setItem("highscore",JSON.stringify(score.value))
    console.log("NEWHIGHSCOREWRITTEN",score.value);
  }
}
</script>

<template>
  <header id="dices">
    <button v-if="trowsCount<3" @click.prevent="throwDice()"><DiceIcon/></button>
    <div class="oneDice" 
      v-for="(oneDice, index) in dice" 
      :class="diceLock[index]?'locked':''"
      @click.prevent="lockDice(index)">
      {{ oneDice }}
    </div>
    <button @click.prevent="newGame()"><ReloadIcon/></button>
  </header>
    <main>
    <article id="points">
    <GenericInput v-for="index in 6" :variable="results[index-1]" :specificfunction="()=>countCertainkind(index)" :text="index" :compareValue="3*index"/>
    </article>
    <article id="points2">
    <GenericInput :variable="results2.fullHouse" :specificfunction="()=>checkFullHouse()" text="FullHouse" compareValue="25"/>
    <GenericInput :variable="results2.smallStreet" :specificfunction="()=>checkSmallStreet()" text="smallStreet" compareValue="30"/>
    <GenericInput :variable="results2.bigStreet" :specificfunction="()=>checkBigStreet()" text="bigStreet" compareValue="40"/>
    <GenericInput :variable="results2.equals[0]" :specificfunction="()=>checkEquals(3)" text="equals3" compareValue="12"/>
    <GenericInput :variable="results2.equals[1]" :specificfunction="()=>checkEquals(4)" text="equals4" compareValue="16"/>
    <GenericInput :variable="results2.equals[2]" :specificfunction="()=>checkEquals(5)" text="Kniffel" compareValue="50"/>
    <GenericInput :variable="results2.chance" :specificfunction="()=>setChance()" text="Chance" compareValue="16"/>
    </article>
    <article>
      <span class="wider">
        highscore: {{ highscore }}
      </span>
      <span :class="score<=highscore?'red wider':'green wider'">your Score: {{ score }}</span>
    </article>
  </main>
  <!-- <pre>{{ results }}</pre>
  <pre>{{ results2 }}</pre> -->
</template>