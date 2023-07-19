<template>
  <div id="app">
    <h1>Kart Eşleştirme Oyunu</h1>
    <InputCardCount v-if="!gameStarted" @start="startGame" />
    <CardGame
      v-else
      :cardCount="cardCount"
      :gameOver="gameOver"
      :score.sync="score"
      @resetScore="resetScore"
      @gameOver="endGame"
    />
    <transition name="slide-fade">
      <div v-if="gameOver" class="game-over">
        <div class="game-over-content">
          <h2>Oyun Bitti!</h2>
          <p>Elde ettiğiniz skor: {{ score }}</p>
          <button @click="restartGame">Yeniden Başla</button>
        </div>
      </div>
    </transition>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';
import InputCardCount from './components/InputCardCount.vue';
import CardGame from './components/CardGame.vue';

@Component({
  components: {
    InputCardCount,
    CardGame,
  },
})
export default class App extends Vue {
  gameStarted: boolean = false;
  gameOver: boolean = false;
  cardCount: number = 6;
  score: number = 0;

  startGame(cardCount: number): void {
    this.cardCount = cardCount;
    this.gameStarted = true;
    this.gameOver = false;
  }

  resetScore(): void {
    this.score = 0;
  }

  endGame(score: number): void {
    this.gameOver = true;
    this.score = score;
  }

  restartGame(): void {
    this.resetScore();
    this.gameOver = false;
    this.gameStarted = false;
  }
}
</script>

<style>
#app {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  margin-top: 20px;
}

h1 {
  margin-bottom: 20px;
}

button {
  margin-top: 10px;
}

.game-over {
  text-align: center;
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100vh;
  display: flex;
  flex-direction: column;
  align-content: center;
  justify-content: center;
  background-color: green;
  background-image: url(./assets/confetti.gif);
  color: #333;
  background-position: center;
  background-size: contain;
}
.game-over-content {
  max-width: 300px;
  width: 100%;
  margin: 0 auto;
  background-color: white;
  padding: 40px;
  border-radius: 20px;
}
.game-over h2 {
  font-size: 24px;
  margin-bottom: 10px;
}

.game-over p {
  font-size: 18px;
  margin-bottom: 20px;
}

.game-over button {
  font-size: 18px;
  padding: 8px 16px;
  background-color: blue;
  border: none;
  color: #fff;
  border-radius: 4px;
  cursor: pointer;
}

.slide-fade-enter-active {
  transition: all 0.3s ease;
}
.slide-fade-leave-active {
  transition: all 0.8s cubic-bezier(1, 0.5, 0.8, 1);
}
.slide-fade-enter, .slide-fade-leave-to
/* .slide-fade-leave-active below version 2.1.8 */ {
  transform: translateX(100%);
  opacity: 0;
}
</style>
