<script setup>
import { ref } from "vue";
import LetterBox from "@/components/LetterBox.vue";
import KeyboardSection from "@/components/KeyboardSection.vue";
import MessageBox from "@/components/MessageBox.vue";

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
const word = ref("");
const messageBox = ref("");
      getNewWord();
function range(start, end) {
  let returnValue = [];
  for (let i = start; i < end; i++) {
    returnValue.push(i);
  }
  return returnValue;
}
function doGuess(a) {
  let letters = a.split("");
  let w = word.value.split("");
  for (let i = 0; i < NUM_LETTERS; i++) {
    letterValues.value[round.value][i] = letters[i];
    if (letterValues.value[round.value][i] == w[i]) {
      colorValues.value[round.value][i] = "green";
    } else if (w.indexOf(letterValues.value[round.value][i]) > -1) {
      colorValues.value[round.value][i] = "yellow";
    } else {
      colorValues.value[round.value][i] = "gray";
    }
  }
  console.log(letterValues.value);
  round.value++;
  checkWin();
}
function checkWin() {
  let lastAnswer = "";
  letterValues.value[round.value - 1].forEach((e) => {
    lastAnswer = lastAnswer + e;
  });
  if (lastAnswer == word.value) {
    setWinState(1);
  } else if (round.value >= NUM_ROWS) {
    setWinState(0);
  }
}
function setWinState(code) {
  if (code == 1) {
    // you win
    messageBox.value = "You won. Hooray.";
  } else if (code == 0) {
    // you lose
    messageBox.value = "You lost. Oh no. The word was " + word.value + ", by the way.";
  }
}

function getNewWord() {
  let newWord;
  fetch("https://random-word-api.herokuapp.com/word?length=5")
    .then((response) => (response = response.json()))
    .then((response) => {
      newWord = response[0];
      word.value = newWord;
    })
    .catch((err) => console.error(err));
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
    />
    <button @click="getNewWord">New word</button>
    <MessageBox :message="messageBox" />
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
  min-width: 2.63em;
  min-height: 2.63em;
  border: solid black 3px;
  /* margin: 0em; */
  text-align: center;
}
</style>
