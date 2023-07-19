<template>
  <div>
    <label for="cardCount">Kart Sayısı (Sadece çift sayı girebilirsiniz):</label>
    <input type="number" id="cardCount" v-model.number="cardCount" @input="validateCardCount" />
    <button @click="startGame" :disabled="!isCardCountValid">Oyuna Başla</button>
  </div>
</template>

<script lang="ts">
import { Component, Vue, Emit } from 'vue-property-decorator';

@Component
export default class InputCardCount extends Vue {
  cardCount: number = 6;
  isCardCountValid: boolean = true;

  validateCardCount(): void {
    this.isCardCountValid = this.cardCount % 2 === 0;
  }

  @Emit('start')
  startGame(): number {
    return this.cardCount;
  }
}
</script>
