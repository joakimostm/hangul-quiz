<template>
  <div id="app">
    <main>
      <p class="questionclass" v-bind:title="answer">{{ question }}</p>
      <input type="text" v-focus placeholder="Input romanization" v-model="input" @keyup.enter="checkAnswer"/>
      <p v-if="hasAnswered && wrong">Wrong! The correct answer was {{ answer }}</p>
      <p v-else></p>
      <br>
      <a>Difficulty:</a>
      <br>
      <input type="radio" id="checkeasy" value="easy" v-model="difficulty" @change="newHangul">
      <label for="checkeasy" title="Only vowels">Easy</label>
      <input type="radio" id="checknormal" value="normal" v-model="difficulty" @change="newHangul">
      <label for="checknormal" title="No trailing consonants">Normal</label>
      <input type="radio" id="checkhard" value="hard" v-model="difficulty" @change="newHangul">
      <label for="checkhard" title="Both leading and trailing consonants">Hard</label>
    </main>
  </div>
</template>

<script>
import Vue from 'vue'
var hangulRomanization = require('kroman')

Vue.directive('focus', {
  inserted: function (el) {
    el.focus()
  }
})

export default {
  name: 'App',
  components: {},
  data () {
    return {
      question: '',
      input: '',
      answer: '',
      hasAnswered: false,
      wrong: false,
      difficulty: 'normal'
    }
  },
  methods: {
    newHangul () {
      var lead
      this.difficulty === 'easy' ? lead = 12 : lead = Math.floor(Math.random() * 18) + 1
      var vowel = Math.floor(Math.random() * 20) + 1
      var tail
      this.difficulty === 'hard' ? tail = Math.floor(Math.random() * 26) + 1 : tail = 0
      var hangul = tail + (vowel - 1) * 28 + (lead - 1) * 588 + 44032
      this.question = String.fromCharCode(hangul)
      this.answer = hangulRomanization.parse(this.question)
      this.hasAnswered = false
      this.wrong = true
      this.input = ''
    },
    checkAnswer () {
      if (this.hasAnswered) {
        this.newHangul()
        return
      }
      this.answer = hangulRomanization.parse(this.question)
      this.hasAnswered = true
      this.wrong = !(this.input === this.answer)
      if (!this.wrong) {
        this.newHangul()
      }
    }
  },
  mounted () {
    this.newHangul()
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
.questionclass {
  font-size: 85px;
  margin: 0px;
}
</style>
