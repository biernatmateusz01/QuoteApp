<template>
  <div
    class="bg-white p-6 flex flex-col justify-between shadow-md rounded-md w-72 sm:w-96 min-h-[400px] hover:bg-cyan-700 hover:text-white transition duration-300 ease-in-out"
  >
    <div class="flex justify-between">
      <BaseText>Draw your quote</BaseText>
      <BaseText
        >We have
        <span class="text-cyan-500">{{ allQuotes.length }}</span>
        quotes</BaseText
      >
    </div>

    <div class="flex flex-col">
      <span class="block m-auto text-center text-cyan-500">{{
        selectedQuote.quote
      }}</span>
      <span class="block m-auto text-sm text-center mt-2 text-cyan-500">{{
        selectedQuote.author
      }}</span>
    </div>

    <div class="flex justify-end">
      <!-- <BaseButton>prev</BaseButton> -->
      <BaseButton @click="getNextQuote">next</BaseButton>
    </div>
  </div>
</template>

<script setup>
import BaseButton from "./BaseButton.vue";
import BaseText from "./BaseText.vue";
import axios from "axios";
import { ref } from "vue";
const allQuotes = ref([]);
const selectedQuote = ref([]);

const fetchQuotes = () => {
  axios
    .get(
      "https://gist.githubusercontent.com/natebass/b0a548425a73bdf8ea5c618149fe1fce/raw/f4231cd5961f026264bb6bb3a6c41671b044f1f4/quotes.json"
    )
    .then((response) => {
      allQuotes.value = response.data;
      selectedQuote.value = allQuotes._rawValue[0];
    })
    .catch((error) => {
      console.log(error);
    });
};

fetchQuotes();

const getNextQuote = () => {
  selectedQuote.value =
    allQuotes._rawValue[Math.floor(Math.random() * allQuotes._rawValue.length)];
};
</script>
