<script setup>
import { ref, watchEffect, watch } from "vue";
import LetterBox from "@/components/LetterBox.vue";

const alphabet = [
  "a",
  "b",
  "c",
  "d",
  "e",
  "f",
  "g",
  "h",
  "i",
  "j",
  "k",
  "l",
  "m",
  "n",
  "o",
  "p",
  "q",
  "r",
  "s",
  "t",
  "u",
  "v",
  "w",
  "x",
  "y",
  "z",
];
const inputValue = ref("");

const guess = ref("");
const greens = ref([]);
const yellows = ref([]);
const grays = ref([]);
const emit = defineEmits(["submission"]);
const props = defineProps({
  round: Number,
  letterValues: [String][String],
  word: String,
  message: Function,
});
function submitGuess() {
  if (inputValue.value.length == props.word.length) {
    guess.value = inputValue.value;
    inputValue.value = "";

    //  check definition:
    fetch(`https://api.dictionaryapi.dev/api/v2/entries/en/${guess.value}`)
      .then((response) => (response = response.json()))
      .then((response) => {
        if (response[0].word == guess.value) {
          emit("submission", guess.value);
          let result = checkAnswer();
          colorAlphabet(result);
        }
      })
      .catch(() => {
        props.message(guess.value + " is not a recognized word.");
      });
  }
}
function checkAnswer() {
  let result = new Array(props.letterValues[props.round].length);
  let currentGuess = props.letterValues[props.round];
  let answer = props.word.split("");
  console.log(props.round + "answer...: " + answer);
  currentGuess.forEach((element, index) => {
    console.log(element + "," + index);
    if (element == answer[index]) {
      result[index] = "green";
    } else if (answer.indexOf(element) > -1) {
      result[index] = "yellow";
    } else {
      result[index] = "gray";
    }
  });
  return result;
}
function colorAlphabet(result) {
  let currentGuess = props.letterValues[props.round];
  result.forEach((element, index) => {
    if (element == "green") {
      if (greens.value.indexOf(currentGuess[index]) == -1) {
        greens.value.push(currentGuess[index]);
        yellows.value = yellows.value.filter((e) => {
          return e != currentGuess[index];
        });
      }
    } else if (element == "yellow") {
      if (yellows.value.indexOf(currentGuess[index]) == -1) {
        yellows.value.push(currentGuess[index]);
      }
    } else if (element == "gray") {
      if (grays.value.indexOf(currentGuess[index]) == -1) {
        grays.value.push(currentGuess[index]);
      }
    }
  });
}
function color(letter) {
  let returnValue = "";
  if (greens.value.indexOf(letter) > -1) returnValue = "green";
  else if (yellows.value.indexOf(letter) > -1) returnValue = "yellow";
  else if (grays.value.indexOf(letter) > -1) returnValue = "gray";
  return returnValue;
}
function resetGame() {
  inputValue.value = "";
  guess.value = "";
  greens.value = [];
  yellows.value = [];
  grays.value = [];
}

watchEffect(() => {
  if (props.round == 0) resetGame();
});
watchEffect(() => {
  if (inputValue.value.length > props.word.length) {
    inputValue.value = inputValue.value.substring(0, props.word.length);
  }
});

function clickLetter(letter) {
  inputValue.value = inputValue.value + letter;
}
function backspace() {
  inputValue.value = inputValue.value.substring(0, inputValue.value.length - 1);
}
</script>

<template>
  <div>
    <main>
      <div id="letters">
        <LetterBox
          v-for="character in alphabet"
          :letter="character"
          :key="character"
          :id="`alphabetBox` + character"
          :color="color(character)"
          @click="clickLetter(character)"
        />
      </div>
      <div id="inputRow">
        <input
          autocomplete="off"
          id="guessInput"
          placeholder="Guess a word"
          v-model="inputValue"
          @keyup.enter="submitGuess()"
        />
        <button @click="backspace()">&lt;&lt;</button>
        <button @click="submitGuess()">Submit</button>
      </div>
    </main>
  </div>
</template>

<style scoped>
#letters {
  display: flex;
}
main {
  /* width: 420px; */
  display: flex;
  flex-direction: column;
}
button {
  background-color: inherit;
  color: inherit;
  /* font-size: 2em; */
  border: black solid;
}
#inputRow {
  width: 100%;
  display: flex;
  /* justify-content: space-evenly; */
}
input {
  background-color: inherit;
  color: inherit;
  font-size: 2em;
  width: 10.2em;
  border: black solid;
}
.letterBox {
  font-size: 1em;
  min-width: 1em;
  min-height: 1em;
  border: solid black 3px;
  /* margin: 0em; */
  text-align: center;
  cursor: pointer;
}
</style>
