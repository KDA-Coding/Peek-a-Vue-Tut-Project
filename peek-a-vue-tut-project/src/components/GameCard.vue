<template>
    <div class="card" :class="flippedStyles" @click="selectCard">
        <div v-if="visible" class="card-face is-front">
            <img :src="`/images/${value}.png`"  :alt="value"/>
            <img v-if="matched" 
            src="../../public/images/checkmark.svg"
            class="icon-checkmark"/>
        </div>
        <div v-else class="card-face is-back"></div>
    </div>
</template>

<script>

    import { computed } from 'vue'

export default {

    props: {
        position: {
            type: Number,
            required: true
        },
        value: {
            type: String,
            required: true
        },
        visible: {
            type: Boolean,
            default: false
        },
        matched: {
            type: Boolean,
            default: false

        }
    },

    setup (props, context) {

        const flippedStyles = computed(() => {
            if(props.visible){
                return 'is-flipped'
            }
        })

        const selectCard =  () => {
            context.emit('select-card', {
                position: props.position,
                faceValue: props.value

            })
        }

        return { selectCard, flippedStyles }
    }
}
</script>

<style>

.card {
  position: relative;

  transition: 0.5s transform ease-in;
}

.card.is-flipped {

    transform: rotateY(180deg);

}

.card-face {
    width: 100%;
    height: 100%;
    position: absolute;
    border-radius: 10px;
    display: flex;
    align-items: center;
    justify-content: center;
}

.card-face.is-front {
    background-image: url('../../public/images/card-bg.png');
    color: white;
}

.card-face.is-back {
    background-image: url('../../public/images/card-bg-empty.png');
    color: white;
}

.icon-checkmark {
    position: absolute;
    right: 5px;
    bottom: 5px;
}

</style>