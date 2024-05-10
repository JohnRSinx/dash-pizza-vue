<script setup>
import { ref } from 'vue'
import PizzaIcon from '../assets/pizza-duotone.svg'


defineProps({
  msg: String,
})

const count = ref(0)
</script>

<template>
  <div className="p-4 flex flex-row justify-between items-center border-b border-zinc-800">
    <h1 className=" text-xl ">{{ msg }}</h1>
    <img :src="PizzaIcon" alt="" className="size-8">
  </div>
</template>


