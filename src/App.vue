<template>
  <div id="app" class="font-karla antialiased text-white">
    <div class="container">
      <div class="max-w-md mx-auto py-2">
        <div class="flex items-baseline justify-between">
          <div class="font-bold">Turns: {{ turns }}</div>
          <div
            class="bg-yellow-600 font-bold text-sm py-2 px-3 rounded shadow-md"
          >
            Time: {{ min }} : {{ sec }}
          </div>
          <button
            v-if="gameIsfinished"
            @click="resetGame"
            class="bg-yellow-600 hover:bg-yellow-700 text-sm font-bold rounded text-white py-2 px-4"
          >
            Play Again
          </button>
        </div>
        <div class="flex flex-wrap -mx-1 mt-3">
          <div
            v-for="(card, index) in memoryCards"
            :key="index"
            class="w-1/4 mb-1 md:mb-2 px-1 flip-container cursor-pointer"
            :class="{ flipped: card.isFlipped, 'opacity-25': card.isMatched }"
            @click="flipCard(card)"
          >
            <div class="relative">
              <div class="front">
                <img width="100" src="./assets/images/back.png" />
              </div>
              <div class="back">
                <img
                  width="100"
                  :src="require(`./assets/images/${card.image}`)"
                />
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      cards: [
        {
          name: 'Ace',
          image: 'ace.png'
        },
        {
          name: 'Diamond',
          image: 'diamond.png'
        },
        {
          name: 'Flower',
          image: 'flower.png'
        },
        {
          name: 'Judge',
          image: 'judge.png'
        },
        {
          name: 'King',
          image: 'king.png'
        },
        {
          name: 'Love',
          image: 'love.png'
        },
        {
          name: 'Queen',
          image: 'queen.png'
        },
        {
          name: 'Nine',
          image: 'nine.png'
        }
      ],
      memoryCards: [],
      flippedCards: [],
      gameIsfinished: false,
      start: false,
      turns: 0,
      totalTime: {
        minutes: 0,
        seconds: 0
      }
    }
  },
  created() {
    this.resetGame()
  },
  computed: {
    sec() {
      if (this.totalTime.seconds < 10) {
        return '0' + this.totalTime.seconds
      }
      return this.totalTime.seconds
    },
    min() {
      if (this.totalTime.minutes < 10) {
        return '0' + this.totalTime.minutes
      }
      return this.totalTime.minutes
    }
  },
  methods: {
    flipCard(card) {
      if (card.isMatched || card.isFlipped || this.flippedCards.length === 2)
        return

      if (!this.start) {
        this.startGame()
      }

      card.isFlipped = true
      if (this.flippedCards.length < 2) {
        this.flippedCards.push(card)
      }
      if (this.flippedCards.length === 2) {
        this.matchCard()
      }
    },
    matchCard() {
      this.turns++

      if (this.flippedCards[0].name === this.flippedCards[1].name) {
        setTimeout(() => {
          this.flippedCards.forEach((card) => (card.isMatched = true))
          this.flippedCards = []

          if (this.memoryCards.every((card) => card.isMatched === true)) {
            clearInterval(this.interval)
            this.gameIsfinished = true
          }
        }, 400)
      } else {
        setTimeout(() => {
          this.flippedCards.forEach((card) => (card.isFlipped = false))
          this.flippedCards = []
        }, 800)
      }
    },
    startGame() {
      this.tick()
      this.interval = setInterval(this.tick, 1000)
      this.start = true
    },
    tick() {
      if (this.totalTime.seconds !== 59) {
        this.totalTime.seconds++
        return
      }

      this.totalTime.minutes++
      this.totalTime.seconds = 0
    },
    resetGame() {
      clearInterval(this.interval)

      this.cards.forEach((card) => {
        this.$set(card, 'isFlipped', false)
        this.$set(card, 'isMatched', false)
      })

      setTimeout(() => {
        this.memoryCards = []
        this.memoryCards = this.$_.shuffle(
          this.memoryCards.concat(
            this.$_.cloneDeep(this.cards),
            this.$_.cloneDeep(this.cards)
          )
        )
        this.totalTime.minutes = 0
        this.totalTime.seconds = 0
        this.turns = 0
        this.gameIsfinished = false
        this.start = false
        this.flippedCards = []
      }, 600)
    }
  }
}
</script>

<style>
@import 'assets/css/tailwind.css';

body {
  background: #0c0d15;
}

.flip-container {
  perspective: 1000;
}

.front,
.back {
  backface-visibility: hidden;
  transition: 0.6s;
  transform-style: preserve-3d;
  top: 0;
  left: 0;
  width: 100%;
}
.back {
  transform: rotateY(-180deg);
  position: absolute;
}

.flip-container.flipped .back {
  transform: rotateY(0deg);
}
.flip-container.flipped .front {
  transform: rotateY(180deg);
}
</style>
