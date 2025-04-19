<script setup>
import HelloWorld from './components/HelloWorld.vue'
import {ref,onMounted} from 'vue'
import PocketBase from 'pocketbase'

const hi = "life better on"
const isTrue = ref(true)
const solatAppLink = "https://www.e-solat.gov.my/index.php?r=esolatApi/takwimsolat&period=today&zone=SGR01"
const weather = "https://api.msn.com/weather/overview?apikey=j5i4gDqHL6nGYwx5wi5kRhXjtf2c5qgFX9fzfk0TOo&activityId=734A851E-34CA-4956-A5D1-FFBA388EB1E4&ocid=msftweather&cm=en-my&it=edgeid&user=m-0019B882ACC36FA40869ABB7AD546E8A&scn=APP_ANON&units=C&appId=9e21380c-ff19-4c78-b4ea-19558e93a5d3&wrapodata=false&includemapsmetadata=true&cuthour=true&lifeDays=2&lifeModes=18&includestorm=true&includeLifeActivity=true&lifeSubTypes=1%2C3%2C4%2C10%2C26&insights=65536&startDate=-1&endDate=%2B9&discardFutureInsightTimeseries=true&distanceinkm=0&regionDataCount=20&orderby=distance&days=10&pageOcid=prime-weather%3A%3Aweathertoday-peregrine&source=weather_csr&fdhead=PRG-1SW-WXWPDEL%2CPRG-1SW-WXWPDS%2CPRG-1SW-WXWPTLI%2Cprg-1sw-wxaqifct%2C1s-wxshortcache%2Cprg-1sw-wxaqepp%2C1s-wxlongcache%2Cprg-1sw-wxaqoff%2C1s-wxmixcache%2Cprg-1sw-wxncvf%2Cprg-1sw-wxtrlog%2CPRG-1SW-WXINSV2%2CPRG-1SW-WXOIVT&region=my&market=en-my&locale=en-my&lat=3.256535768508911&lon=101.73859405517578"
const pb = new PocketBase ('http://127.0.0.1:8090')
const records = ref([])
const todoInput = ref("")

let name = ref("")
let number = ref(0);
let prayerTime = ref({})
let weather1 = ref({})
let capitalCities = ref(["England","Paris","Rome","kuala Lumpur"])


function increment(){
  number.value++
  console.log(number)
}
function startInterval(){
  setInterval(increment,1000)
}
function addCity() {
  if (name.value.trim() !== "") {
    capitalCities.value.push(name.value)
    name.value = "" // Clear the input after adding
  }
}
function getPrayerTime(){
  fetch(solatAppLink).then(result => result.json()). then(data =>{prayerTime.value = data.prayerTime[0]
    console.log(data.prayerTime)})
}
function getweather(){
  fetch(weather).then(result => result.json()).then(data =>{weather1.value = data.responses[0].weather[0]; console.log(weather1.value)
  })
}
function createTodo(){
  const newTodo = {
    item : todoInput,value,
    isDone : false
  }

pb.collection('todos').create(newTodo).then(res => {
  records.value.push(res)
  todoInput.value = ""
})
}
onMounted(()=>{
  getPrayerTime()
  pb.collection('todos').getFullList({}).then(data => records.value=data);
})
</script>

<template>
  <div class="min-h-screen bg-gradient-to-br from-blue-50 to-indigo-100 p-6 flex flex-col items-center">
    <!-- Counter Section -->
    <div class="w-full max-w-lg bg-white rounded-lg shadow-lg p-6 mb-8">
      <div class="flex items-center justify-between">
        <h2 class="text-3xl font-bold text-indigo-700">{{ number }}</h2>
        <button 
          @click="startInterval" 
          class="px-4 py-2 bg-indigo-600 text-white rounded-md hover:bg-indigo-700 transition-colors focus:outline-none focus:ring-2 focus:ring-indigo-500 focus:ring-opacity-50"
        >
          Increment
        </button>
      </div>
    </div>

    <!-- City Management Section -->
    <div class="w-full max-w-lg bg-white rounded-lg shadow-lg p-6 mb-8">
      <h2 class="text-xl font-semibold text-gray-800 mb-4">Add New City</h2>
      <div class="flex gap-3">
        <input 
          type="text" 
          v-model="name" 
          placeholder="Enter city name" 
          class="flex-1 px-4 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500"
        >
        <button 
          @click="addCity" 
          class="px-4 py-2 bg-green-600 text-white rounded-md hover:bg-green-700 transition-colors focus:outline-none focus:ring-2 focus:ring-green-500 focus:ring-opacity-50"
        >
          Add City
        </button>
      </div>

      <div class="mt-6">
        <h2 class="text-2xl font-bold text-gray-800">{{ hi }}</h2>
        <h3 class="text-lg text-gray-600 mb-4">{{ name }}</h3>
        
        <div v-if="true" class="p-4 bg-gray-100 rounded-md mb-4">
          <h3 class="text-xl font-medium text-gray-800">England is my city</h3>
          <button 
            @click="isTrue = isTrue" 
            class="mt-2 px-3 py-1 bg-gray-300 text-gray-700 rounded hover:bg-gray-400 text-sm transition-colors"
          >
            Toggle
          </button>
        </div>

        <h3 class="font-medium text-gray-700 mb-2">Capital Cities:</h3>
        <ol class="bg-gray-50 rounded-md p-4 list-decimal list-inside space-y-2">
          <li 
            v-for="city in capitalCities" 
            class="text-gray-700 hover:text-indigo-700 transition-colors"
          >
            {{ city }}
          </li>
        </ol>
      </div>
    </div>

    <!-- Prayer Time Section -->
    <div class="w-full max-w-lg bg-white rounded-lg shadow-lg p-6 mb-8">
      <div class="flex justify-between items-center mb-4">
        <h2 class="text-xl font-semibold text-gray-800">Prayer Times</h2>
        <button 
          @click="getPrayerTime" 
          class="px-4 py-2 bg-teal-600 text-white rounded-md hover:bg-teal-700 transition-colors focus:outline-none focus:ring-2 focus:ring-teal-500 focus:ring-opacity-50"
        >
          Get Prayer Times
        </button>
      </div>
      
      <div class="border rounded-lg bg-teal-50 shadow p-4 flex items-center space-x-4">
        <div class="bg-teal-100 p-3 rounded-full">
          <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6 text-teal-700" fill="none" viewBox="0 0 24 24" stroke="currentColor">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 3v1m0 16v1m9-9h-1M4 12H3m15.364 6.364l-.707-.707M6.343 6.343l-.707-.707m12.728 0l-.707.707M6.343 17.657l-.707.707M16 12a4 4 0 11-8 0 4 4 0 018 0z" />
          </svg>
        </div>
        <div>
          <h3 class="font-medium text-teal-800">Fajr</h3>
          <p class="text-xl font-bold text-teal-900">{{ prayerTime.fajr || '--:--' }}</p>
        </div>
      </div>
    </div>

    <!-- Weather Section -->
    <div class="w-full max-w-lg bg-white rounded-lg shadow-lg p-6">
      <div class="flex justify-between items-center mb-4">
        <h2 class="text-xl font-semibold text-gray-800">Weather Forecast</h2>
        <button 
          @click="getweather" 
          class="px-4 py-2 bg-blue-600 text-white rounded-md hover:bg-blue-700 transition-colors focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-opacity-50"
        >
          Get Weather
        </button>
      </div>
      
      <div class="bg-gradient-to-r from-blue-100 to-blue-200 rounded-lg p-4">
        <h3 class="text-lg font-medium text-blue-800 mb-1">Today's Weather</h3>
        <div v-if="weather1.current" class="flex items-center justify-between">
          <div class="flex items-center">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-10 w-10 text-yellow-500 mr-3" fill="none" viewBox="0 0 24 24" stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 3v1m0 16v1m9-9h-1M4 12H3m15.364 6.364l-.707-.707M6.343 6.343l-.707-.707m12.728 0l-.707.707M6.343 17.657l-.707.707M16 12a4 4 0 11-8 0 4 4 0 018 0z" />
            </svg>
            <span class="text-2xl font-bold text-blue-900">{{ weather1.current.cap }}</span>
          </div>
          <span class="text-3xl font-bold text-blue-900">{{ weather1.current.temp }}°C</span>
        </div>
        <div v-else class="text-blue-800 text-center py-2">
          Click to load weather data
        </div>
      </div>
    </div>
  </div>
  <h1>Todos</h1>
  <ul>
    <button @click="records">add to do list</button>
    <li v-for="todo in records" :key="todo.id">
      {{ todo.item }} 
    </li>
  </ul>
</template>

<style scoped>
.logo {
  height: 6em;
  padding: 1.5em;
  will-change: filter;
  transition: filter 300ms;
}
.logo:hover {
  filter: drop-shadow(0 0 2em #646cffaa);
}
.logo.vue:hover {
  filter: drop-shadow(0 0 2em #42b883aa);
}
</style>
