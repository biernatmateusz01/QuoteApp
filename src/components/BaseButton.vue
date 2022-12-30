<template>
    <button :class="close ? 'hover:text-red-600' : 'text-cyan-500'" class="text-red hover:bg-cyan-500 rounded-sm hover:text-white px-4 py-2 bg-white border border-cyan-500 text-cyan-500 transition duration-300 ease-in-out"><slot></slot></button>
</template>

<script setup>
defineProps({
  close: Boolean,
})
</script>