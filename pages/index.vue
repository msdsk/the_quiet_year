<template>
  <section :class="seasons[currentSeason]">
    <h1>The quiet year</h1>
    <div v-if="!gameOver">
      <section>
        <h2>Current season:</h2>
        <div>
          {{seasons[currentSeason]}}, week {{(currentWeek + 1)}}
        </div>
        <button class="btn" type="button" @click="drawCard">Draw a card</button>
      </section>
      <section v-if="currentCard">
        <h2>Current card:</h2>
        <div class="card">
          <div v-for="(option, i) in currentCard.options" :key="i">
            <div class="divider" v-if="i!==0"><span>or</span></div>
            <!-- Option w stripped initial whitespace -->
            <div class="option">{{option.replace(/^\s*/g,'')}}</div>
          </div>
        </div>
      </section>
    </div>
    <div v-else>
      The Frost Shepherds arrive. The game is over.
    </div>
  </section>
</template>

<script>
import cards from "~/components/cards.json";
export default {
  data() {
    return {
      seasons: ["spring", "summer", "autumn", "winter"],
      currentSeason: 0,
      currentWeek: -1,
      sortedCards: {},
      currentCard: null,
      willEndInWeek: Math.floor(Math.random() * 13),
      gameOver: false
    };
  },
  methods: {
    drawCard() {
      if (this.currentWeek < 12) {
        this.currentWeek++;
      } else {
        this.currentWeek = 0;
        this.currentSeason++;
      }

      //ending the game early during winter
      if (this.currentSeason === 3 && this.currentWeek === this.willEndInWeek) {
        this.gameOver = true;
      }

      let currentSeasonName = this.seasons[this.currentSeason];
      this.currentCard = this.sortedCards[currentSeasonName][this.currentWeek];
    }
  },
  beforeMount() {
    let sortedCards = {};

    /**
     * @param {*[]} array Array to shuffle
     */
    function shuffle(array) {
      for (let i = array.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1));
        [array[i], array[j]] = [array[j], array[i]]; // eslint-disable-line no-param-reassign
      }
      return array;
    }

    this.seasons.forEach(season => {
      sortedCards[season] = shuffle(cards[season].slice());
    });
    this.sortedCards = sortedCards;
  }
};
</script>

<style>
.option {
  white-space: pre-line;
}
</style>
