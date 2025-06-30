<template>
  <div class="jetlag-app">
    <h1>Jet Lag Minimizer</h1>
    <form @submit.prevent="handleSubmit">
      <div>
        <label>Outbound Flight Number:</label>
        <input v-model="outbound" placeholder="e.g. WS123" required />
      </div>
      <div>
        <label>Inbound Flight Number:</label>
        <input v-model="inbound" placeholder="e.g. WS456" required />
      </div>
      <div>
        <label>Departure Date & Time:</label>
        <input v-model="departure" type="datetime-local" required />
      </div>
      <div>
        <label>Arrival Date & Time:</label>
        <input v-model="arrival" type="datetime-local" required />
      </div>
      <div>
        <label>Departure Timezone (e.g. America/Edmonton):</label>
        <input v-model="departureTz" placeholder="e.g. America/Edmonton" required />
      </div>
      <div>
        <label>Arrival Timezone (e.g. Europe/London):</label>
        <input v-model="arrivalTz" placeholder="e.g. Europe/London" required />
      </div>
      <button type="submit">Suggest Sleep Schedule</button>
    </form>
    <div v-if="schedule">
      <h2>Suggested Sleep Schedule</h2>
      <ul>
        <li v-for="(item, idx) in schedule" :key="idx">{{ item }}</li>
      </ul>
    </div>
    <div v-if="longestFlight">
      <h2>Flight Duration Comparison</h2>
      <p>Outbound ({{ outbound }}): {{ outboundDurationStr }}</p>
      <p>Inbound ({{ inbound }}): {{ inboundDurationStr }}</p>
      <p><strong>Longest Flight:</strong> {{ longestFlight }}</p>
    </div>
  </div>
</template>


<script setup>
import { ref, computed } from 'vue'

const outbound = ref('')
const inbound = ref('')
const departure = ref('')
const arrival = ref('')
const departureTz = ref('')
const arrivalTz = ref('')
const schedule = ref(null)

const outboundDeparture = ref('')
const outboundArrival = ref('')
const inboundDeparture = ref('')
const inboundArrival = ref('')

// For this demo, outbound = user input, inbound = reverse (swap dep/arr)
const outboundDuration = computed(() => {
  if (!departure.value || !arrival.value) return 0
  const dep = new Date(departure.value)
  const arr = new Date(arrival.value)
  return Math.max(0, arr - dep)
})

const inboundDuration = computed(() => {
  if (!arrival.value || !departure.value) return 0
  // Simulate inbound as reverse (for demo)
  const dep = new Date(arrival.value)
  const arr = new Date(departure.value)
  return Math.max(0, arr - dep)
})

const outboundDurationStr = computed(() => formatDuration(outboundDuration.value))
const inboundDurationStr = computed(() => formatDuration(inboundDuration.value))

const longestFlight = computed(() => {
  if (outboundDuration.value === 0 && inboundDuration.value === 0) return null
  if (outboundDuration.value > inboundDuration.value) return `Outbound (${outbound.value})`
  if (inboundDuration.value > outboundDuration.value) return `Inbound (${inbound.value})`
  if (outboundDuration.value === inboundDuration.value && outboundDuration.value !== 0) return 'Both flights are the same duration'
  return null
})

function formatDuration(ms) {
  if (!ms) return 'N/A'
  const totalMinutes = Math.floor(ms / 60000)
  const hours = Math.floor(totalMinutes / 60)
  const minutes = totalMinutes % 60
  return `${hours}h ${minutes}m`
}

function handleSubmit() {
  schedule.value = [
    `3 days before departure: Go to bed 1 hour earlier each night`,
    `On the flight: Try to sleep according to your destination's night time`,
    `After arrival: Expose yourself to sunlight in the morning, avoid naps`
  ]
}
</script>

<style scoped>
.jetlag-app {
  max-width: 540px;
  margin: 40px auto;
  padding: 2.5rem;
  background: #fff;
  border-radius: 14px;
  box-shadow: 0 2px 12px rgba(0,0,0,0.10);
  color: #1a1a1a;
  font-size: 1.15rem;
  line-height: 1.7;
}
.jetlag-app h1 {
  text-align: center;
  margin-bottom: 2rem;
  font-size: 2rem;
  color: #003366;
}
.jetlag-app h2 {
  margin-top: 2rem;
  font-size: 1.3rem;
  color: #005fa3;
}
.jetlag-app form > div {
  margin-bottom: 1.3rem;
}
.jetlag-app label {
  display: block;
  margin-bottom: 0.4rem;
  font-weight: 600;
  color: #222;
  letter-spacing: 0.01em;
}
.jetlag-app input {
  width: 100%;
  padding: 0.65rem;
  border: 1.5px solid #888;
  border-radius: 5px;
  font-size: 1.08rem;
  background: #f5f7fa;
  color: #1a1a1a;
  transition: border-color 0.2s, box-shadow 0.2s;
}
.jetlag-app input:focus {
  outline: 2px solid #005fa3;
  border-color: #005fa3;
  background: #fff;
  box-shadow: 0 0 0 2px #b3d4fc;
}
.jetlag-app button {
  width: 100%;
  padding: 0.9rem;
  background: #005fa3;
  color: #fff;
  border: none;
  border-radius: 5px;
  font-size: 1.1rem;
  font-weight: 600;
  cursor: pointer;
  margin-top: 1.2rem;
  letter-spacing: 0.02em;
  transition: background 0.2s, box-shadow 0.2s;
}
.jetlag-app button:hover, .jetlag-app button:focus {
  background: #003366;
  box-shadow: 0 2px 8px rgba(0,0,0,0.10);
}
.jetlag-app ul {
  padding-left: 1.2em;
  margin-bottom: 0;
}
.jetlag-app li {
  margin-bottom: 0.5em;
}
.jetlag-app p, .jetlag-app label, .jetlag-app input, .jetlag-app button {
  font-family: system-ui, Arial, sans-serif;
}
</style>
