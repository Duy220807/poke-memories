
<template>
    <div class="screen">
        <div class="screen_inner" :style="{
            width: `${((((920 - 16 * 4) / Math.sqrt(cardsContext.length) - 16) * 3) / 4 + 16) * Math.sqrt(cardsContext.length)}px`,
        }">
            <card-flip v-for="(card, index) in cardsContext" :key="index" :ref="`card-${index}`"
                :cardsContext="cardsContext" :imgBackFaceUrl="`images/${card}.png`" :card="{ index, value: card }"
                :rules="rules" @onFlip="checkRule($event)" />
        </div>
    </div>
</template>

<script>
import { ref } from "vue";
import CardFlip from "./Card.vue"
export default {
    props: {
        cardsContext: {
            type: Array,
            default: function () {
                return [];
            }
        }
    },
    components: {
        CardFlip
    },
    data() {
        return {
            rules: ref([])
        }
    },
    methods: {
        checkRule(card) {
            if (this.rules.length === 2) return false;

            this.rules.push(card)


            if (this.rules.length === 2 && this.rules[0].value === this.rules[1].value) {

                this.$refs[`card-${this.rules[0].index}`][0].onEnableDisableMode();
                this.$refs[`card-${this.rules[1].index}`][0].onEnableDisableMode();
                this.rules = [];

                const disabledElements = document.querySelectorAll('.screen .card.disabled');

                // console.log(disabledElements)

                if (disabledElements && disabledElements.length === this.cardsContext.length - 2) {

                    setTimeout(() => {
                        this.$emit('onFinish')
                    }, 1000)

                }
            }
            else if (this.rules.length === 2 && this.rules[0].value !== this.rules[1].value) {


                setTimeout(() => {
                    this.$refs[`card-${this.rules[0].index}`][0].onFlipBackCard();
                    this.$refs[`card-${this.rules[1].index}`][0].onFlipBackCard();
                    this.rules = [];
                }, 800)

            }

            else return false;
        }
    }
}
</script>

<style lang="css" scoped>
.screen {
    width: 100vw;
    height: 100vh;
    position: absolute;
    top: 0;
    left: 0;
    z-index: 2;
    background-color: var(--dark);
    color: var(--light);
}

.screen_inner {

    display: flex;
    flex-wrap: wrap;
    margin: 2rem auto;


}
</style>