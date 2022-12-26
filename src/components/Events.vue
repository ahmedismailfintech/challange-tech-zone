<template>
  <main class="events">
    <div class="events__timer">
      <div
        v-for="(time, index) in times"
        :key="index"
        class="events__timer__time"
      >
        <p v-if="index % 2 != 0">
          {{ time.replace(/AM|PM/g, "") }}
        </p>
        <p v-else>
          {{ time }}
        </p>
      </div>
    </div>
    <div class="events__container">
      <template v-for="({ start, end }, index) in events" :key="index">
        <eventItem
          :style="{
            transform: `translateY(${start}px)`,
            height: `${end - start}px`
          }"
        />
      </template>
    </div>
  </main>
</template>

<script setup>
import { transform } from "@vue/compiler-core";
import { defineAsyncComponent } from "@vue/runtime-core";
import { reactive, onMounted } from "vue";

const eventItem = defineAsyncComponent(() => import("./EventItem.vue"));
let x = 30; //minutes interval
let times = []; // time array
const events = reactive([]);

const getEvents = async () => {
  const res = await fetch("http://localhost:3000/events");
  const data = await res.json();
  events.push(...data);
};
onMounted(() => {
  getEvents();
});
for (let i = 0; i <= 720; i++) {
  const date = new Date(new Date().setHours(9));
  if (i % x == 0) {
    date.setMinutes(i, 0, 0);
    times.push(
      date.toLocaleTimeString([], { hour: "numeric", minute: "2-digit" })
    );
  }
}
</script>
