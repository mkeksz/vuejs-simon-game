<template>
  <div id="app">
    <h1>Simon The Game</h1>
    <div class="game">
      <div>
        <div class="circle">
          <button
            :disabled="disabledCircles"
            class="circle__button"
            data-num="1"
            @mousedown="mouseDownCircleHandler"
            @mouseup="mouseUpCircleHandler"
            @mouseleave="mouseLeaveCircleHandler"
          ></button>
          <button
            :disabled="disabledCircles"
            class="circle__button"
            data-num="2"
            @mousedown="mouseDownCircleHandler"
            @mouseup="mouseUpCircleHandler"
            @mouseleave="mouseLeaveCircleHandler"
          ></button>
          <button
            :disabled="disabledCircles"
            class="circle__button"
            data-num="3"
            @mousedown="mouseDownCircleHandler"
            @mouseup="mouseUpCircleHandler"
            @mouseleave="mouseLeaveCircleHandler"
          ></button>
          <button
            :disabled="disabledCircles"
            class="circle__button"
            data-num="4"
            @mousedown="mouseDownCircleHandler"
            @mouseup="mouseUpCircleHandler"
            @mouseleave="mouseLeaveCircleHandler"
          ></button>
        </div>
      </div>
      <div>
        <div class="panel">
          <h3>Round: {{ round }}</h3>
          <button @click="clickStartHandler">Start</button>
          <h3>Difficulty</h3>
          <div class="panel__difficulty">
            <label><input type="radio" value="0" v-model="difficulty" /> Easy </label>
            <label><input type="radio" value="1" v-model="difficulty" /> Normal </label>
            <label><input type="radio" value="2" v-model="difficulty" /> Hard </label>
          </div>
        </div>
      </div>
    </div>
    <div class="github">
      <a href="https://github.com/mkeksz/vuejs-simon-game">Go to GitHub</a>
    </div>
  </div>
</template>

<script>
export default {
  data: () => ({
    gameIsStart: false,
    round: 0,
    difficulty: 0,
    disabledCircles: false,
    queueCircles: [],
    currentQueueCircles: [],
    circles: null,
    timeDifficulty: {
      0: 1500,
      1: 1000,
      2: 400,
    },
  }),
  methods: {
    clickStartHandler() {
      this.gameIsStart = true
      this.round = 1
      this.disabledCircles = true
      this.queueCircles = []
      this.roundStart()
    },

    roundStart() {
      this.currentQueueCircles = []
      const randNumCircle = Math.round(Math.random() * (Object.keys(this.circles).length - 1) + 1)
      this.queueCircles.push(randNumCircle.toString())
      setTimeout(() => this.queueNext(0), this.timeDifficulty[this.difficulty])
    },
    roundEnd() {
      this.disabledCircles = false
    },
    roundNext() {
      this.disabledCircles = true
      this.round++
      this.roundStart()
    },
    queueNext(idx) {
      const numCircle = this.queueCircles[idx],
        circle = this.circles[numCircle]

      this.soundPlay(numCircle)
      this.glowStart(circle)
      setTimeout(() => {
        this.glowEnd(circle)
        if (this.queueCircles.length <= idx + 1) {
          this.roundEnd()
          return
        }
        this.queueNext(idx + 1)
      }, this.timeDifficulty[this.difficulty])
    },

    gameOver() {
      this.round = 0
      this.gameIsStart = false
      alert('You failed')
    },

    mouseDownCircleHandler(e) {
      const circle = e.target
      this.soundPlay(circle.dataset.num)
      this.glowStart(circle)
    },
    mouseUpCircleHandler(e) {
      const circle = e.target
      this.glowEnd(circle)
      if (this.gameIsStart) this.checkQueue(circle.dataset.num)
    },
    mouseLeaveCircleHandler(e) {
      const circle = e.target
      this.glowEnd(circle)
    },

    glowStart(circle) {
      circle.classList.add('glow')
    },
    glowEnd(circle) {
      circle.classList.remove('glow')
    },
    soundPlay(numSound) {
      // const audio = document.getElementById(`media-${numSound}`)
      // console.log({ audio }, numSound)
      // audio.currentTime = 0
      // audio.play()
      let audio = new Audio(`${__webpack_public_path__}media/${numSound}.ogg`)
      audio.play()
      audio.remove()
    },

    checkQueue(numCircle) {
      this.currentQueueCircles.push(numCircle)
      this.currentQueueCircles.forEach((val, i) => {
        if (val !== this.queueCircles[i]) this.gameOver()
      })
      if (this.currentQueueCircles.length !== this.queueCircles.length) return
      if (this.currentQueueCircles.toString() === this.queueCircles.toString()) this.roundNext()
    },
  },
  mounted() {
    this.circles = {
      '1': document.querySelector('.circle__button[data-num="1"]'),
      '2': document.querySelector('.circle__button[data-num="2"]'),
      '3': document.querySelector('.circle__button[data-num="3"]'),
      '4': document.querySelector('.circle__button[data-num="4"]'),
    }
  },
}
</script>

<style lang="sass">
*
  font-family: inherit
  box-sizing: border-box

body
  margin: 0
  padding: 20px 0
  font-family: Roboto, Noto Sans, -apple-system, BlinkMacSystemFont, sans-serif
button
  border: none
button:focus
  outline: none
h1
  text-align: center

.github
  position: absolute
  text-align: center
  bottom: 15px
  width: 100%

.game
  padding-top: 40px
  display: flex
  flex-direction: row
  justify-content: space-around
  max-width: 600px
  margin: auto
  user-select: none

.panel
  >h3
    margin: 20px 0 10px
  >button
    background-color: #6dabe8
    padding: 10px 20px
    font-size: 16px
    text-transform: uppercase
    color: #fff
    border-radius: 6px
    cursor: pointer
    opacity: .6
    transition: opacity .2s
  >button:hover
    opacity: 1
.panel__difficulty
  display: flex
  flex-direction: column


.circle
  width: 300px
  height: 300px
  display: grid
  grid-template-areas: 'a b' 'c d'
  >button
    width: 100%
    height: 100%
    cursor: pointer
    transition: border-color .1s, opacity .3s
    opacity: .6
  >button:hover
    border-color: #6e6e6e
  >button.glow
    opacity: 1
    box-shadow: 0 0 10px #fff
    z-index: 2

.circle__button[data-num="1"]
  background-color: dodgerblue
  border-top-left-radius: 100%
  border-top: 2px solid transparent
  border-left: 2px solid transparent
.circle__button[data-num="2"]
  background-color: #FF5643
  border-top-right-radius: 100%
  border-top: 2px solid transparent
  border-right: 2px solid transparent
.circle__button[data-num="3"]
  background-color: #FEEF33
  border-bottom-left-radius: 100%
  border-bottom: 2px solid transparent
  border-left: 2px solid transparent
.circle__button[data-num="4"]
  background-color: #BEDE15
  border-bottom-right-radius: 100%
  border-bottom: 2px solid transparent
  border-right: 2px solid transparent
@media (max-width: 425px)
  .circle
    width: 200px
    height: 200px
</style>
