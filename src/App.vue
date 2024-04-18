<template>

  <section class="bg-gray-900 min-h-screen">
    <div class="py-8 px-4 mx-auto max-w-screen-xl lg:py-16">
      <div class="flex flex-col justify-center mb-8">
        <h1 class="mb-4 text-4xl font-extrabold text-white tracking-tight leading-none md:text-4xl lg:text-5xl">
          Fast Fingers
        </h1>
        <p class="text-lg font-normal text-gray-400 lg:text-xl">
          Improve your typing speed with Fast Fingers.
        </p>
      </div>

      <div v-if="isFinished" class="mb-4 bg-gray-700 p-6 rounded-lg" role="alert">
        <h2 class="mb-4 font-bold text-4xl text-white leading-tight">Time is up!</h2>
        <hr class="border-gray-100 opacity-30" />
        <p class="my-4 font-bold text-4xl text-green-600">{{ trueCounter }} WPM</p>
        <div class="flex flex-col my-4">
          <div class="flex flex-row mb-2">
            <p class="w-24 font-bold text-lg text-white">Accuracy:</p>
            <p class="font-bold text-lg text-green-500">{{ accuracy }}</p>
          </div>
          <div class="flex flex-row mb-2">
            <p class="w-36 font-bold text-lg text-white">Correct Words:</p>
            <p class="font-bold text-lg text-green-500">{{ trueCounter }}</p>
          </div>
          <div class="flex flex-row">
            <p class="w-36 font-bold text-lg text-white">Wrong Words:</p>
            <p class="font-bold text-lg text-red-500">{{ falseCounter }}</p>
          </div>
        </div>
        <button class="bg-green-700 w-full p-3 font-bold text-md text-white rounded-lg hover:bg-green-600" @click="restart">Play Again</button>
      </div>
      <div v-else>
        <div class="bg-gray-500 w-full rounded-lg mb-4 pt-5 pb-6 px-6 flex flex-row flex-wrap">
        <span v-for="(word, key) in words.filter((data, index) => index < 23)" :key="key"
              v-bind:class="key === 0 ? wordControl : null"
              class="text-bold text-xl lg:text-2xl text-white p-2 ml-1 rounded-lg">
          {{ word }}
        </span>
        </div>
        <div class="bg-gray-800 w-full lg:min-w-screen p-6 space-y-8 sm:p-8 rounded-lg shadow-xl">
          <div class="space-y-6">
            <div class="flex flex-row justify-between items-center">
              <input type="text" name="words" id="words" class="bg-gray-700 border-gray-600 text-white lg:text-xl rounded-lg block w-full p-2.5 focus:outline-none" v-model="currentWord">
              <button type="button" class="text-gray-600 bg-white font-bold rounded-lg lg:text-xl px-5 py-2.5 mx-2 focus:outline-none" disabled>{{ time }}</button>
              <a href="/" type="button" class="text-white bg-blue-600 hover:bg-blue-500 font-bold rounded-lg lg:text-xl px-5 py-2.5 focus:outline-none disabled:opacity-50 disabled:pointer-events-none" :disabled="isStarted" @click="getWords">
                <svg class="w-7 h-7 text-white" aria-hidden="true" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 20 18">
                  <path stroke="currentColor" stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="m1 14 3-3m-3 3 3 3m-3-3h16v-3m2-7-3 3m3-3-3-3m3 3H3v3"/>
                </svg>
              </a>
            </div>
          </div>
        </div>
      </div>
    </div>
  </section>

</template>

<script>

import wordList from './assets/words.json'

export default {
  data() {
    return {
      time: 60,
      interval: false,
      isStarted: false,
      isFinished: false,
      words: [],
      wordList: wordList,
      currentWord: null,
      isTrue: true,
      trueCounter: 0,
      falseCounter: 0,
    }
  },

  watch: {
    currentWord: function (val) {
      if(!val || val === ' ') {
        this.currentWord = ''
        return
      }
      if(!this.isStarted) { this.startTimer() }
      const word = this.words[0].slice(0, val.length)
      this.isTrue = word === val.replace(' ', '')
      if (val.indexOf(' ') !== -1) {
        this.isTrue ? this.trueCounter++ : this.falseCounter++
        this.words.splice(0, 1)
        this.currentWord = ''
        this.isTrue = true
      }
    }
  },

  computed: {
    wordControl() {
      return this.isTrue ? 'bg-gray-800' : 'bg-red-500'
    },
    wpm() {
      return (300 - this.words.length)
    },
    accuracy() {
      const percent =  (100 / this.wpm)
      const accuracy = (percent * this.trueCounter)
      return isNaN(accuracy) ? 0 + '%' : accuracy.toFixed(2) + '%'
    }
  },

  mounted () {
    this.getWords()
  },

  methods: {

    getWords() {
      this.words = this.wordList.sort(() => Math.random() - 0.5).splice(0, 300)
    },

    startTimer() {
      this.isStarted = true
      this.interval = setInterval(this.timer, 1000)
    },

    timer() {
      if(this.time === 0) {
        this.isFinished = true
        clearInterval(this.interval)
        return
      }
      this.time--
    },

    restart() {
      this.getWords()
      this.time= 60
      this.isStarted= false
      this.isFinished= false
      this.currentWord= ''
      this.isTrue= true
    }
  },

}
</script>

<style scoped>
</style>
