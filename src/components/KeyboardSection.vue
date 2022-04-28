<script setup>
import { ref, computed } from "vue";
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
const emit = defineEmits(["submission", "colorChange"]);
const props = defineProps({
  round: Number,
  letterValues: [String][String],
  word: String,
});
function submitGuess() {
  guess.value = inputValue.value;
  inputValue.value = "";
  emit("submission", guess.value);
  let result = checkAnswer();
  console.log(result);
  colorAlphabet(result);
  console.log(
    "greens: " +
      greens.value +
      "  yellows: " +
      yellows.value +
      "  grays: " +
      grays.value
  );
  // TODO: now need to set alphabet letters background colors based on colors stored in result array
}
function checkAnswer() {
  let result = new Array(props.letterValues[props.round].length);
  let currentGuess = props.letterValues[props.round];
  let answer = props.word.split("");
  currentGuess.forEach((element, index) => {
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
  emit("colorChange", { letter: letter, color: returnValue });
  return returnValue;
}

console.log(props.letterValues);
</script>

<template>
  <div>
    <main>
      <LetterBox
        v-for="character in alphabet"
        :letter="character"
        :key="character"
        :id="`alphabetBox` + character"
        :color="color(character)"
      />
    </main>
    <input
      id="guessInput"
      placeholder="Guess a word"
      v-model="inputValue"
      @keyup.enter="submitGuess()"
    />
    input: {{ inputValue }} round: {{ props.round }}
  </div>
</template>

<style>
main {
  display: flex;
}
input {
  background-color: inherit;
  color: inherit;
}
.letterBox {
  font-size: 1em;
  min-width: 1em;
  min-height: 1em;
  border: solid black 3px;
  /* margin: 0em; */
  text-align: center;
}
</style>
