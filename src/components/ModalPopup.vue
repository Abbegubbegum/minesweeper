<template>
    <Transition name="modal">
        <div v-if="show" class="modal-mask">
            <div class="modal-wrapper">
                <div class="modal-container">
                    <h2 class="title">{{ title }}</h2>

                    <div class="timer-container">
                        <h3 class="timer">{{ timerLabel }}</h3>
                        <h3 class="timer">Highscore: {{ highScoreLabel }}</h3>
                    </div>

                    <button
                        class="modal-default-button"
                        @click="$emit('close')"
                    >
                        OK
                    </button>
                </div>
            </div>
        </div>
    </Transition>
</template>

<script lang="ts">
import moment from "moment";
import { defineComponent } from "vue";

export default defineComponent({
    data() {
        return {};
    },
    props: {
        show: { type: Boolean, required: true },
        win: { type: Boolean, required: true },
        seconds: { type: Number, required: true },
        highscore: { type: Number, required: false },
    },
    computed: {
        title() {
            return this.win ? "You win!" : "You lose!";
        },
        timerLabel() {
            if (this.win)
                return (
                    "Your time: " +
                    moment()
                        .startOf("hour")
                        .second(this.seconds)
                        .format("mm:ss")
                );
        },
        highScoreLabel() {
            if (!this.highscore) return "-";

            return moment()
                .startOf("hour")
                .second(this.highscore)
                .format("mm:ss");
        },
    },
});
</script>

<style>
.modal-mask {
    position: fixed;
    z-index: 9998;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: rgba(0, 0, 0, 0.5);
    transition: opacity 0.3s ease;
}

.modal-wrapper {
    position: fixed;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
}

.modal-container {
    width: 200px;
    padding: 30px;
    background-color: #fff;
    border-radius: 2px;
    box-shadow: 0 2px 8px rgba(0, 0, 0, 0.33);
    transition: all 0.3s ease;
    display: grid;
    grid-auto-flow: row;
    justify-content: center;
}

.title {
    text-align: center;
}

.timer {
    text-align: center;
    margin: 1rem;
}
/*
 * The following styles are auto-applied to elements with
 * transition="modal" when their visibility is toggled
 * by Vue.js.
 *
 * You can easily play with the modal transition by editing
 * these styles.
 */

.modal-enter-from {
    opacity: 0;
}

.modal-leave-to {
    opacity: 0;
}

.modal-enter-from .modal-container,
.modal-leave-to .modal-container {
    -webkit-transform: scale(1.1);
    transform: scale(1.1);
}
</style>
