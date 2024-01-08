<template>
  <main>
    <div class="flex flex-col">
      <h1 class="text-center rounded bg-gray-700 text-white p-3 font-bold mb-3">
        Switch colors and win !
      </h1>
      <hr />
      <div class="my-3">
        <label for="">Level:</label>
        <select
          v-model="level"
          :class="{
            'rounded mx-2 text-white p-1': true,
            'bg-green-500': level === 'easy',
            'bg-blue-500': level === 'midium',
            'bg-red-500': level === 'hard',
          }"
        >
          <option
            v-for="level in levels"
            :key="level.name"
            :class="level.tailwindClass"
            :value="level.name"
          >
            {{ level.name }}
          </option>
        </select>
      </div>
      <h1
        v-show="counter != 0 && couples.length != rightAnswers()"
        class="my-2 text-center rounded p-2"
      >
        {{ counter }} chances left !
      </h1>
      <h1
        v-show="couples.length == rightAnswers()"
        class="text-center rounded bg-green-700 text-white p-3 font-bold"
      >
        You won 🥳 !
      </h1>
      <h1
        v-show="counter == 0 && couples.length != rightAnswers()"
        class="text-center rounded bg-orange-700 text-white p-3 font-bold"
      >
        You lost this party, retry !
      </h1>

      <div class="font-bold text-center my-3">
        {{ rightAnswers() }}/{{ couples.length }} right colors !
      </div>

      <div class="flex flex-wrap justify-between my-3">
        <div
          @click="select(couple._id)"
          v-for="couple in couples"
          :key="couple._id"
          :style="{
            backgroundColor: couple.current,
            opacity: 0.8,
            color: 'white',
          }"
          :class="{
            'm-1 rounded shadow-lg p-2 flex flex-col cursor-pointer vinghtpourcent md:centpx': true,
            'border-4 border-green-500':
              couple.current === couple.expected && false,
            'border-4 border-red-500':
              couple.current !== couple.expected && false,
          }"
        >
          <!--box-->
          <svg
            xmlns="http://www.w3.org/2000/svg"
            fill="none"
            viewBox="0 0 24 24"
            stroke-width="1.5"
            stroke="currentColor"
            :class="{
              'bg-white text-black rounded': true,
            }"
            :style="{
              backgroundColor: counter == 0 ? couple.expected : 'white',
            }"
          >
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              d="m20.25 7.5-.625 10.632a2.25 2.25 0 0 1-2.247 2.118H6.622a2.25 2.25 0 0 1-2.247-2.118L3.75 7.5M10 11.25h4M3.375 7.5h17.25c.621 0 1.125-.504 1.125-1.125v-1.5c0-.621-.504-1.125-1.125-1.125H3.375c-.621 0-1.125.504-1.125 1.125v1.5c0 .621.504 1.125 1.125 1.125Z"
            />
          </svg>
          <div class="flex justify-center">
            <!-- selected-->
            <svg
              v-if="couple._id == selected1 || couple._id == selected2"
              xmlns="http://www.w3.org/2000/svg"
              fill="none"
              viewBox="0 0 24 24"
              stroke-width="1.5"
              stroke="currentColor"
              class="h-6 w-4 hover:text-red-500"
            >
              <path
                stroke-linecap="round"
                stroke-linejoin="round"
                d="m4.5 12.75 6 6 9-13.5"
              />
            </svg>
            <!-- waiting to be selected-->
            <svg
              v-else
              xmlns="http://www.w3.org/2000/svg"
              fill="none"
              viewBox="0 0 24 24"
              stroke-width="1.5"
              stroke="currentColor"
              class="h-6 w-4 hover:text-red-500"
            >
              <path
                stroke-linecap="round"
                stroke-linejoin="round"
                d="M12 6v6h4.5m4.5 0a9 9 0 1 1-18 0 9 9 0 0 1 18 0Z"
              />
            </svg>
          </div>
        </div>
      </div>
      <div class="text-center">
        <button
          v-show="helpsNumber != 0"
          class="bg-pink-700 hover:bg-blue-500 rounded p-2 text-white"
          @click="help"
        >
          Help
        </button>
        <button
          v-show="couples.length != rightAnswers() && counter != 0"
          class="bg-blue-700 hover:bg-blue-500 mx-2 rounded p-2 text-white"
          @click="switchSelected"
        >
          Switch
        </button>
        <button
          class="bg-green-700 hover:bg-green-500 rounded p-2 text-white"
          @click="reset"
        >
          Replay
        </button>
      </div>
    </div>
  </main>
</template>

<script setup>
import { ref, onMounted, watch } from "vue";

const levels = ref([
  {
    name: "easy",
    tailwindClass: "bg-green-500",
    colors: ["red", "green", "blue", "yellow"],
  },
  {
    name: "midium",

    tailwindClass: "bg-blue-500",

    colors: ["red", "green", "blue", "yellow", "purple", "orange"],
  },
  {
    name: "hard",
    tailwindClass: "bg-red-500",

    colors: [
      "red",
      "green",
      "blue",
      "yellow",
      "purple",
      "orange",
      "gray",
      "pink",
    ],
  },
]);

const couples = ref([]);
const selected1 = ref(null);
const selected2 = ref(null);
const counter = ref(15);
const level = ref("easy");
const helpsNumber = ref(1);
const help = () => {
  if (helpsNumber.value === 0) {
    return;
  }
  const couple = couples.value.find((couple) => couple._id === selected1.value);
  const oldCurrentColor = couple.current;

  couple.current = couple.expected;
  const newCurrentColor = couple.current;
  const oldCouple = couples.value.find(
    (couple) =>
      couple.current === newCurrentColor && couple._id !== selected1.value
  );
  if (oldCouple) {
    oldCouple.current = oldCurrentColor;
  }

  helpsNumber.value--;
  selected1.value = null;
};
watch(level, () => {
  reset();
});
onMounted(() => {
  reset();
});
const rightAnswers = () => {
  return couples.value.filter((couple) => couple.current === couple.expected)
    .length;
};
const select = (id) => {
  //diselcting
  if (selected1.value === id) {
    selected1.value = null;
    return;
  } else if (selected2.value === id) {
    selected2.value = null;
    return;
  }
  //selecting
  if (selected1.value === null) {
    selected1.value = id;
    return;
  } else if (selected2.value === null) {
    selected2.value = id;
    return;
  }
};
const switchSelected = () => {
  //tow couples must be selected
  if (selected1.value === null || selected2.value === null) {
    return;
  }
  //finding couples
  const couple1 = couples.value.find(
    (couple) => couple._id === selected1.value
  );
  const couple2 = couples.value.find(
    (couple) => couple._id === selected2.value
  );
  //switching
  const temp = couple1.current;
  couple1.current = couple2.current;
  couple2.current = temp;
  selected1.value = null;
  selected2.value = null;
  counter.value--;
};

const reset = () => {
  let colors = [];
  const levelColors = levels.value.find(
    (levelIt) => levelIt.name === level.value
  ).colors;
  colors = levelColors;
  const shufflColors1 = colors.slice().sort(() => Math.random() - 0.5);
  const shufflColors2 = colors.slice().sort(() => Math.random() - 0.5);
  //reset couples
  couples.value = [];
  for (let i = 0; i < colors.length; i++) {
    console.log("current:" + shufflColors1[i], "expected:" + shufflColors2[i]);
    couples.value.push({
      _id: i,
      current: shufflColors1[i],
      expected: shufflColors2[i],
    });
  }
  selected1.value = null;
  selected2.value = null;
  counter.value = parseInt(2 * couples.value.length);
};
</script>

<style scoped>
main {
  height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
}
.vinghtpourcent {
  width: 20%;
  height: 20%;
}
.centpx {
  width: 100px;
  height: 100px;
}
</style>
