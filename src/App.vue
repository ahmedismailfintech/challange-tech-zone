<script setup>
import { defineAsyncComponent } from "@vue/runtime-core";
const Events = defineAsyncComponent(() => import("./components/Events.vue"));
</script>

<template>
  <Events />
</template>
