<template>
<div class="h-screen flex items-center justify-center bg-gradient-to-t bg">
  <div class="dark:bg-slate-800 p-3 rounded-lg" v-if="errorMsg.length > 0">
    <p class="text-white">{{ errorMsg }}</p>
    <button @click="returnToHome()" type="button" class=" mt-3 py-3.5 px-5 mr-2 mb-2 text-sm font-medium text-gray-900 focus:outline-none bg-white rounded-lg dark:bg-gray-700 dark:text-gray-400 dark:border-gray-600 dark:hover:text-white dark:hover:bg-gray-700">Search</button>
  </div>
  <div v-if="!(errorMsg.length > 0)" class="dark:bg-slate-800 p-3 rounded-lg">
    <div v-if="resultName && displayItems.length > 0">
    <img class="block mx-auto" :src="displayItems[2].img">
    <b v-for="item in displayItems" :key="item">
      <div class="dark:bg-gray-700 leading-10 h-10 w-96 rounded-sm mb-3 text-gray-200 border-t-2  border-gray-500 ...">
        <p><i class="ml-2" :class="item.icon"></i> {{item.name}} <font class="float-right mr-4">{{item.value}}{{item.unit}}</font></p>
      </div>
    </b>
    <button @click="returnToHome()" type="button" class=" mt-3 py-3.5 px-5 mr-2 mb-2 text-sm font-medium text-gray-900 focus:outline-none bg-white rounded-lg dark:bg-gray-700 dark:text-gray-400 dark:border-gray-600 dark:hover:text-white dark:hover:bg-gray-700">Search</button>
  </div>
  <div v-else>
  <form @keydown.enter.prevent="search()">
    <input type="search" id="fname" name="fname" class="block w-full p-4 pl-10 text-sm text-gray-900 border border-gray-300 rounded-lg bg-gray-50 focus:ring-blue-500 focus:border-blue-500 dark:bg-gray-700 dark:border-gray-600 dark:placeholder-gray-400 dark:text-white dark:focus:ring-blue-500 dark:focus:border-blue-500" placeholder="Search for a location..." required v-model="queriedLocation">
  </form> 
   <button @click="search()" type="button" class=" mt-3 py-3.5 px-5 mr-2 mb-2 text-sm font-medium text-gray-900 focus:outline-none bg-white rounded-lg dark:bg-gray-700 dark:text-gray-400 dark:border-gray-600 dark:hover:text-white dark:hover:bg-gray-700">Search</button>
  </div>
  </div>
</div>
</template>

<script>
export default {
  data() {
    return {
      errorMsg: "",
      queriedLocation: "",
      resultName: "",
      displayItems: []
    }
  },
  methods: {
    formatItem(name) {
      return name = name[0].toUpperCase() + name.substring(1);
    },
    async search() {
      if(this.queriedLocation.length > 0) {
        const apiKey = "b2c5eeb06177482f96e150152232306"; // Insert your API key
        const response = await fetch(`https://api.weatherapi.com/v1/current.json?key=${apiKey}&q=${this.queriedLocation.toLowerCase()}`);
        const data = await response.json();
        if(data.error != undefined) {
          this.errorMsg = data.error.message;
        } else {
          this.resultName = data.location.name;
          this.displayItems.push({name: "Temperature", value: data.current.temp_c, icon: "fa-solid fa-temperature-three-quarters", unit: "°C"});
          this.displayItems.push({name: "Country", value: `${data.location.country}`, icon: "fa-solid fa-globe"});
          this.displayItems.push({name: "Weather Condition", value: data.current.condition.text, icon: "fa-solid fa-cloud", img: data.current.condition.icon});
          this.displayItems.push({name: "Wind Speed / Direction", value: `${data.current.wind_mph}mph / ${data.current.wind_dir} `, icon: "fa-solid fa-wind"});
          this.displayItems.push({name: "Time", value: data.location.localtime, icon: "fa-solid fa-clock"});
          this.displayItems.push({name: "Humidity", value: data.current.humidity, icon: "fa-solid fa-droplet", unit: "%"});
        }
      }
    },
    returnToHome() {
      this.errorMsg = "", this.queriedLocation = "", this.displayItems = [];
    }
  }
}
</script>
<style>
.bg{
  background-image: url(../assets/weather.jpg);
}
</style>