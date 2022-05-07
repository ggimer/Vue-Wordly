<script setup>
import { ref } from "vue";
import LetterBox from "@/components/LetterBox.vue";
import KeyboardSection from "@/components/KeyboardSection.vue";
import MessageBox from "@/components/MessageBox.vue";

const NUM_ROWS = 9;
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
const round = ref(0);
const messageBox = ref();
const wordLength = ref(NUM_LETTERS);
const word = ref("");

resetGame();

function resetGame() {
  letterValues.value = init2dArray(NUM_ROWS, NUM_LETTERS);
  colorValues.value.forEach((x) => {
    x.forEach((y, index) => {
      x[index] = "";
    });
  });
  round.value = 0;
  messageBox.value =
    "Start the game by guessing a " + NUM_LETTERS + " letter word.";
  getNewWord();
}

function range(start, end) {
  let returnValue = [];
  for (let i = start; i < end; i++) {
    returnValue.push(i);
  }
  return returnValue;
}
function message(msg) {
  messageBox.value = msg;
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
  round.value++;
  message("");
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
    message("You won. Hooray.");
  } else if (code == 0) {
    // you lose
    message("You lost. Oh no. The word was '" + word.value + "', by the way.");
  }
}

function getNewWord() {
  // get random word starting with w with 4 other characters
  // https://api.datamuse.com/words?sp=w????

  let length = wordLength.value;
  let alphabet = "abcdefghijklmnopqrstuvwxyz";
  let newWord;
  let firstChar = alphabet.charAt(Math.floor(Math.random() * alphabet.length));

  let tailString = "";
  for (let i = 0; i < length - 1; i++) {
    tailString = tailString + "?";
  }
  let searchString = firstChar + tailString;

  fetch(`https://api.datamuse.com/words?sp=${searchString}`)
    .then((response) => (response = response.json()))
    .then((response) => {
      let index = Math.floor(Math.random() * response.length);
      newWord = response[index].word;
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
    <button id="resetGameButton" @click="resetGame()">Reset Game</button>
    <KeyboardSection
      :letterValues="letterValues"
      :round="round"
      :word="word"
      :message="message"
      @submission="(msg) => doGuess(msg)"
    />
    <MessageBox :message="messageBox" />
  </div>
</template>

<style scoped>
button {
  background-color: inherit;
  color: inherit;
  border: black solid;
}
#resetGameButton {
  width: 100%;
}
.gameBoardRow {
  display: flex;
  padding: 0.1em;
}
#gameBoard {
  border: 10px black solid;
  width: fit-content;
  height: fit-content;
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
