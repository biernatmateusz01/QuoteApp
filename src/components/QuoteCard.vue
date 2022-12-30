<template>
  <Teleport to="body">
    <transition>
      <BaseModal v-if="isOpenModal">
        <div class="flex relative justify-center h-full items-center flex-col">
          <BaseLoader v-if="isActiveLoader" />
          <BaseButton
            @click="closeModal"
            :close="true"
            class="lg:absolute mb-4 lg:mb-0 lg:top-1 lg:left-1"
            >Close Modal</BaseButton
          >
          <div class="p-4 xl:w-[1200px] min-w-[280px] sm:w-[600px]">
            <BaseTitle>Add new quote</BaseTitle>
            <form @submit.prevent="addNewQuote">
              <div class="flex flex-col gap-2 mb-4 mt-6">
                <div class="flex flex-col">
                  <span>Author</span>
                  <input
                    type="text"
                    v-model="vAuthor"
                    class="border-b border-cyan-400 p-2 text-cyan-400 outline-cyan-400"
                  />
                </div>
                <div class="flex flex-col">
                  <span>Quote</span>
                  <textarea
                    type="text"
                    v-model="vQuote"
                    class="border-b border-cyan-400 p-2 text-cyan-400 outline-cyan-400"
                  ></textarea>
                </div>
              </div>

              <span v-if="wrongValidation" class="text-red-400 my-4 text-center block m-auto"
                >Unfortunately something went wrong <span>&#128542;</span></span
              >
              <input
                type="submit"
                value="Add new quote"
                class="border-b border-cyan-400 p-2 text-cyan-400 block m-auto cursor-pointer hover:bg-cyan-400 hover:text-white transition duration-300 ease-in-out outline-cyan-400"
              />
            </form>
          </div>
        </div>
      </BaseModal>
    </transition>
  </Teleport>
  <div
    class="bg-white p-6 flex flex-col md:text-xl xl:text-2xl justify-between shadow-md rounded-md w-72 md:w-96 lg:min-w-[800px] min-h-[400px] transition duration-300 ease-in-out"
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
      quote: vQuote.value.trimStart(),
      author: vAuthor.value.trimStart(),
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
  wrongValidation.value = false;
  isActiveLoader.value = false;
};
</script>

<style scoped>
.v-enter-active,
.v-leave-active {
  transition: opacity 0.5s ease;
}

.v-enter-from,
.v-leave-to {
  opacity: 0;
}
</style>
