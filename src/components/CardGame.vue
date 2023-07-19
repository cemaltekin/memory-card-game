<template>
  <div>
    <span class="score">Skor: {{ score }}</span>
    <div class="cards">
      <div
        v-for="card in shuffledCards"
        :key="card.id"
        class="card"
        :class="{ flipped: card.flipped, matched: card.matched }"
        @click="flipCard(card)"
      >
        <div class="flipper">
          <div class="front"></div>
          <div class="back" @click.stop="flipCard(card)">
            {{ card.value }}
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Vue, Prop } from 'vue-property-decorator';

interface Card {
  id: number;
  value: number;
  flipped: boolean;
  matched: boolean;
}

@Component
export default class CardGame extends Vue {
  @Prop(Number) readonly cardCount!: number;
  @Prop(Boolean) readonly gameOver!: boolean;
  @Prop(Number) readonly score!: number;

  private cards: Card[] = [];
  private flipTimeout: number | null = null;
  private internalScore: number = 0;
  private fliperable: boolean = true;
  created(): void {
    this.resetScore();
    this.generateCards(this.cardCount);
    this.shuffleCards();
  }

  resetScore(): void {
    this.internalScore = 0;
  }

  generateCards(cardCount: number): void {
    this.cards = [];
    for (let i = 1; i <= cardCount / 2; i++) {
      this.cards.push({ id: i, value: i, flipped: false, matched: false });
      this.cards.push({
        id: i + cardCount / 2,
        value: i,
        flipped: false,
        matched: false,
      });
    }
  }

  shuffleCards(): void {
    for (let i = this.cards.length - 1; i > 0; i--) {
      const j = Math.floor(Math.random() * (i + 1));
      [this.cards[i], this.cards[j]] = [this.cards[j], this.cards[i]];
    }
  }

  flipCard(card: Card): void {
    if (!this.gameOver && !card.flipped && !card.matched && this.fliperable) {
      card.flipped = true;
      this.fliperable = false;
      setTimeout(() => {
        this.fliperable = true;
      }, 300);
      const flippedCards = this.cards.filter((c) => c.flipped && !c.matched);
      if (flippedCards.length === 2) {
        this.checkForMatch(flippedCards);
        this.checkForEndGame();
      }
    }
  }

  checkForMatch(flippedCards: Card[]): void {
    if (flippedCards[0].value === flippedCards[1].value) {
      flippedCards[0].matched = true;
      flippedCards[1].matched = true;
      this.internalScore += 1;
      this.$emit('update:score', this.internalScore);
    } else {
      this.flipTimeout = setTimeout(() => {
        flippedCards.forEach((card) => (card.flipped = false));
        this.flipTimeout = null;
        if (this.internalScore > 0) {
          this.internalScore -= 1;
          this.$emit('update:score', this.internalScore);
        }
      }, 1000);
    }
  }

  checkForEndGame(): void {
    if (this.cards.every((card) => card.matched)) {
      this.$emit('gameOver', this.internalScore);
    }
  }
  beforeDestroy(): void {
    if (this.flipTimeout !== null) {
      clearTimeout(this.flipTimeout);
    }
  }

  get shuffledCards(): Card[] {
    return this.cards.slice().sort(() => Math.random() - 0.5);
  }

  restartGame(): void {
    this.resetScore();
    this.generateCards(this.cardCount);
    this.shuffleCards();
    this.$emit('restart');
  }
}
</script>

<style>
.score {
  font-size: 20px;
  margin-bottom: 20px;
}

.cards {
  display: grid;
  grid-template-columns: repeat(4, minmax(0, 1fr));
  grid-gap: 10px;
  width: 600px;
  margin: 0 auto;
}

.card {
  width: 100%;
  height: 150px;
  perspective: 1000px;
  cursor: pointer;
}

.flipper {
  width: 100%;
  height: 100%;
  transition: transform 0.5s;
  transform-style: preserve-3d;
}

.card.flipped .flipper {
  transform: rotateY(180deg);
}

.front,
.back {
  position: absolute;
  width: 100%;
  height: 100%;
  backface-visibility: hidden;
  display: flex;
  justify-content: center;
  align-items: center;
  font-size: 24px;
  border-radius: 8px;
}

.front {
  background-color: #ddd;
}

.back {
  background-color: #8bd588;
  color: #fff;
  transform: rotateY(180deg);
  cursor: pointer;
}

img {
  max-width: 100%;
  max-height: 100%;
  border-radius: 8px;
}

.game-over {
  text-align: center;
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
  background-color: #8bd588;
  border: none;
  color: #fff;
  border-radius: 4px;
  cursor: pointer;
}
</style>
