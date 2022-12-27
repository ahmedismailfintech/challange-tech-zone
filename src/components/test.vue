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
      <template
        v-for="({ top, height, width, left }, index) in events"
        :key="index"
      >
        <eventItem
          :style="{
            top: `${top}px`,
            left: `${left}px`,
            height: `${height}px`,
            width: `${width}px`
          }"
        />
      </template>
    </div>
  </main>
</template>

<script setup>
import { defineAsyncComponent } from "@vue/runtime-core";
import { reactive, onMounted } from "vue";

const eventItem = defineAsyncComponent(() => import("./EventItem.vue"));
let x = 30; //minutes interval
let times = []; // time array
const container_width = 600 - 32;
const start_from_left = 16;
const events = reactive([]);
let counter = 0;
const getEvents = async () => {
  const res = await fetch("http://localhost:3000/events");
  const data = await res.json();

  const items = data.map((mapEl, mapIndex) => {
    const filterItems = data.filter((el, index) => index !== mapIndex);
    calcRange(filterItems, mapEl);
    // return {
    //   ...el,
    //   left: start_from_left,
    //   top: el.start,
    //   width: container_width,
    //   height: el.end - el.start
    // };
  });
  // calcRange(items);
  events.push(...items);
};
const calcRange = (items, el) => {
  for (let i = 0; i < items.length; i++) {
    if (el["start"] >= items[i]["start"] && el["end"] <= items[i]["end"]) {
    } else if (
      el["start"] <= items[i]["start"] &&
      el["end"] <= items[i]["end"] &&
      el["end"] >= items[i]["start"]
    ) {
    } else if (
      el["start"] >= items[i]["start"] &&
      el["end"] >= items[i]["end"] &&
      el["end"] <= items[i]["start"]
    ) {
    } else if (
      el["start"] <= items[i]["start"] &&
      el["end"] >= items[i]["end"]
    ) {
    }
  }
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
