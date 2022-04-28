<script setup>
import { ref } from "vue";
import LetterBox from "@/components/LetterBox.vue";
import KeyboardSection from "@/components/KeyboardSection.vue";

const NUM_ROWS = 6;
const NUM_LETTERS = 5;

function init2dArray(sizeX, sizeY) {
  let twoDimArray = new Array(sizeX);
  for (let i = 0; i < twoDimArray.length; i++) {
    twoDimArray[i] = new Array(sizeY);
    for (let j = 0; j < sizeY; j++) {
      twoDimArray[i][j] = "";
    }
  }
  return twoDimArray;
}
const letterValues = ref(init2dArray(NUM_ROWS, NUM_LETTERS));
const colorValues = ref(init2dArray(NUM_ROWS, NUM_LETTERS));
console.log("colorValues = " + colorValues.value);
const round = ref(0);
const word = ref("fucky");


function range(start, end) {
  let returnValue = [];
  for (let i = start; i < end; i++) {
    returnValue.push(i);
  }
  return returnValue;
}
function doGuess(a) {
  console.log(a);
  let letters = a.split("");
  console.log(letters);
  for (let i = 0; i < NUM_LETTERS; i++) {
    letterValues.value[round.value][i] = letters[i];
  }
  console.log(letterValues.value);
  round.value++;
}
function setColor(c) {
  if (round.value > 0) {
    for (let i = 0; i < round.value; i++) {
      for (let j = 0; j < NUM_LETTERS; j++) {
        if (letterValues.value[i][j] == c.letter) {
          if (colorValues.value[i][j] == "") {
            colorValues.value[i][j] = c.color;
            console.log(letterValues.value[i][j]);
            console.log(colorValues.value[i][j]);
          }
        }
      }
    }
  }
}
</script>

<template>
  <div id="gameBoard">
    <div
      v-for="num in range(0, NUM_ROWS)"
      class="gameBoardRow"
      :key="num"
      :id="`row` + num"
    >
      <LetterBox
        v-for="box in range(0, NUM_LETTERS)"
        :key="box"
        :letter="letterValues[num][box]"
        :id="`row` + num + `letter` + box"
        :color="colorValues[num][box]"
      />
    </div>
    <KeyboardSection
      :letterValues="letterValues"
      :round="round"
      :word="word"
      @submission="(msg) => doGuess(msg)"
      @colorChange="(returnedValue) => setColor(returnedValue)"
    />
  </div>
</template>

<style scoped>
.gameBoardRow {
  display: flex;
  padding: 0.1em;
}
#gameBoard {
  border: 10px black solid;
  /* width: auto; */
}
.letterBox {
  font-size: 2em;
  min-width: 2em;
  min-height: 2em;
  border: solid black 3px;
  /* margin: 0em; */
  text-align: center;
}
</style>
