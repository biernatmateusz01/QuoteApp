<template>
  <transition>
    <BaseModal v-if="isOpenModal">
      <div class="flex relative justify-center h-full items-center flex-col">
        <BaseLoader v-if="isActiveLoader" />
        <BaseButton
          @click="isOpenModal = !isOpenModal"
          close
          class="lg:absolute mb-4 lg:mb-0 lg:top-1 lg:left-1"
          >Close Modal</BaseButton
        >
        <BaseTitle>Add new quote</BaseTitle>
        <form @submit.prevent="addNewQuote">
          <div class="flex flex-col gap-2 mb-4 mt-6">
            <div class="flex flex-col">
              <span>Author</span>
              <input
                type="text"
                v-model="vAuthor"
                class="border-b border-cyan-400 p-2 text-cyan-400"
              />
            </div>
            <div class="flex flex-col">
              <span>Quote</span>
              <input
                type="text"
                v-model="vQuote"
                class="border-b border-cyan-400 p-2 text-cyan-400"
              />
            </div>
          </div>

          <span v-if="wrongValidation" class="text-red-400 my-4 text-center"
            >Unfortunately something went wrong <span>&#128542;</span></span
          >
          <input
            type="submit"
            value="Add new quote"
            class="border-b border-cyan-400 p-2 text-cyan-400 block m-auto cursor-pointer hover:bg-cyan-400 hover:text-white transition duration-300 ease-in-out"
          />
        </form>
      </div>
    </BaseModal>
  </transition>
  <div
    class="bg-white p-6 flex flex-col justify-between shadow-md rounded-md w-72 sm:w-96 min-h-[400px] hover:bg-cyan-700 hover:text-white transition duration-300 ease-in-out"
  >
    <div class="flex justify-between">
      <BaseText>Draw your quote</BaseText>
      <BaseText
        >We have
        <span class="text-cyan-500">{{ quotesLength }}</span>
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

    <div class="flex justify-between">
      <BaseButton @click="isOpenModal = !isOpenModal">add new</BaseButton>

      <BaseButton @click="getNextQuote">next</BaseButton>
    </div>
  </div>
</template>

<script setup>
import BaseModal from "../components/BaseModal.vue";
import BaseButton from "./BaseButton.vue";
import BaseText from "./BaseText.vue";
import BaseTitle from "./BaseTitle.vue";
import BaseLoader from "./BaseLoader.vue";
import axios from "axios";
import { ref } from "vue";
const allQuotes = ref([]);
const selectedQuote = ref([]);
const isOpenModal = ref(false);
const quotesLength = ref(0);
const vAuthor = ref("");
const vQuote = ref("");
const wrongValidation = ref(false);
const isActiveLoader = ref(false);

const fetchQuotes = () => {
  axios
    .get(
      "https://gist.githubusercontent.com/natebass/b0a548425a73bdf8ea5c618149fe1fce/raw/f4231cd5961f026264bb6bb3a6c41671b044f1f4/quotes.json"
    )
    .then((response) => {
      allQuotes.value = response.data;
      selectedQuote.value =
        allQuotes._rawValue[
          Math.floor(Math.random() * allQuotes._rawValue.length)
        ];
      quotesLength.value = response.data.length;
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

const addNewQuote = () => {
  isActiveLoader.value = true;
  if (vAuthor.value && vQuote.value) {
    allQuotes._rawValue.push({
      author: vAuthor.value.trimStart(),
      quote: vQuote.value.trimStart(),
    });
    setTimeout(() => {
      closeModal();
    }, 2000);
    quotesLength.value = allQuotes.value.length;
    wrongValidation.value = false;


    vAuthor.value = "";
    vQuote.value = "";
  } else {
    wrongValidation.value = true;
    isActiveLoader.value = false;
  }
};

const closeModal = () => {
  isOpenModal.value = false;
  isActiveLoader.value = false;
};
</script>
