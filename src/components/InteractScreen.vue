<template>
  <div class="screen">
    <div
      class="screen__inner"
      :style="{
        width: `${
          ((((920 - 16 * 4) / Math.sqrt(cardsContext.length) - 16) * 3) / 4 + 16) *
          Math.sqrt(cardsContext.length)
        }px`,
      }"
    >
      <card-flip
        v-for="(card, index) in cardsContext"
        :key="index"
        :ref="`card-${index}`"
        :imgBackFaceUrl="`${card}.png`"
        :card="{ index, value: card }"
        :isFlipping="isFlipping"
        :cardsContext="cardsContext"
        @onFlip="checkRule($event)"
      />
    </div>
  </div>
</template>

<script>
import CardFlip from "./Card.vue";
export default {
  name: "InteractScreen",
  components: {
    CardFlip,
  },
  props: {
    cardsContext: {
      type: Array,
      default: function () {
        return [];
      },
    },
  },
  data() {
    return {
      rules: [],
      isFlipping: false,
    };
  },
  methods: {
    checkRule(card) {
      if (this.rules.length === 2) return false;
      this.rules.push(card);

      if (
        this.rules.length === 2 &&
        this.rules[0].value === this.rules[1].value &&
        this.rules[0].index !== this.rules[1].index
      ) {
        this.isFlipping = true;

        //add class 'disabled ' toi component card
        this.$refs[`card-${this.rules[0].index}`][0].onEnableDisabledMode();
        this.$refs[`card-${this.rules[1].index}`][0].onEnableDisabledMode();
        setTimeout(() => {
          this.rules = [];
          this.isFlipping = false;
        }, 800);
        //reset rules to []
        const disabledElements = document.querySelectorAll(
          ".screen .card.disabled"
        );
        if (
          disabledElements &&
          disabledElements.length === this.cardsContext.length - 1
        ) {
          setTimeout(() => {
            this.$emit("onFinish");
          }, 1000);
        }
      } else if (
        this.rules.length === 2 &&
        this.rules[0].value !== this.rules[1].value &&
        this.rules[0].index !== this.rules[1].index
      ) {
        this.isFlipping = true;
        //close two cards

        setTimeout(() => {
          this.$refs[`card-${this.rules[0].index}`][0].onFlipBackCard();
          this.$refs[`card-${this.rules[1].index}`][0].onFlipBackCard();
          this.$refs[`card-${this.rules[0].index}`][0].onDisableDisabledMode();
          this.rules = [];
          this.isFlipping = false;
        }, 800);
      } else if (
        this.rules.length === 2 &&
        this.rules[0].index === this.rules[1].index
      ) {
        this.rules.pop();
      } else {
        this.$refs[`card-${this.rules[0].index}`][0].onEnableDisabledMode();
        return false;
      }
    },
  },
};
</script>

<style lang="css" scoped>
.screen {
  width: 100%;
  height: 100vh;
  position: absolute;
  top: 0;
  left: 0;
  z-index: 2;
  background-color: var(--dark);
  color: var(--light);
}

.screen__inner {
  width: 424px;
  display: flex;
  flex-wrap: wrap;
  margin: 2rem auto;
}
</style>
