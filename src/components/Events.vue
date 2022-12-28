<template>
  <main class="events">
    <ul class="events__timer">
      <template v-for="(time, index) in times" :key="index">
        <li class="events__timer__time">
          <em v-if="index % 2 != 0">
            {{ time.replace(/AM|PM/g, "") }}
          </em>
          <span v-else>
            {{ time }}
          </span>
        </li>
      </template>
    </ul>
    <div class="events__container">
      <template v-for="(event, index) in items" :key="index">
        {{ events }}
        <eventItem
          :style="{
            top: `${event.top}px`,
            left: `${event.left}px`,
            height: `${event.height}px`,
            width: `${event.width}px`
          }"
        />
      </template>
    </div>
  </main>
</template>

<script setup>
import { defineAsyncComponent } from "@vue/runtime-core";
import { reactive } from "vue";

const eventItem = defineAsyncComponent(() => import("./EventItem.vue"));
let x = 30; //minutes interval
let times = []; // time array
const container_width = 600 - 32;
const start_from_left = 16;
const items = reactive([]);

const getEvents = async () => {
  const res = await fetch("http://localhost:3000/events");
  const events = await res.json();
  handleEvents(events);
};
const generateTimeLine = () => {
  for (let i = 0; i <= 720; i++) {
    const date = new Date(new Date().setHours(9));
    if (i % x == 0) {
      date.setMinutes(i, 0, 0);
      times.push(
        date.toLocaleTimeString([], { hour: "numeric", minute: "2-digit" })
      );
    }
  }
};
const handleEvents = (events = []) => {
  let timeSlots = [];
  for (let i = 0; i < 720; i++) {
    timeSlots[i] = [];
  }
  for (let i = 0; i < events.length; i++) {
    let event = events[i];

    for (let j = event.start; j < event.end; j++) {
      timeSlots[j].push(event.id);
    }
  }

  for (let i = 0; i < 720; i++) {
    let next_index = 0;
    let timeSlotsLength = timeSlots[i].length;
    if (timeSlotsLength > 0) {
      for (let j = 0; j < timeSlotsLength; j++) {
        let event = events[timeSlots[i][j] - 1];
        if (
          !event.sharedEventCount ||
          event.sharedEventCount < timeSlotsLength
        ) {
          event.sharedEventCount = timeSlotsLength;

          if (!event.currentIndex) {
            event.currentIndex = next_index;

            next_index++;
          }
        }
      }
    }
  }
  for (let i = 0; i < events.length; i++) {
    let event = events[i];
    event.height = event.end - event.start;
    event.top = event.start;
    event.width = container_width / event.sharedEventCount;
    event.left = event.currentIndex * event.width + start_from_left;
  }
  items.push(...events);
};
getEvents();
generateTimeLine();
</script>
