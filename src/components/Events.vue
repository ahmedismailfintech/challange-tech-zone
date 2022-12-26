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
    <div class="events__container"></div>
  </main>
</template>

<script setup>
let x = 30; //minutes interval
let times = []; // time array

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
