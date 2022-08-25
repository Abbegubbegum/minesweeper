<script setup lang="ts">
import MineGrid from "@/components/MineGrid.vue";
import ModalPopup from "@/components/ModalPopup.vue";
</script>

<template>
    <div class="form-container">
        <div class="input-container">
            <label for="rows">Rows:</label>
            <label for="cols">Columns:</label>
            <label for="bomb-count">Bomb Count:</label>
            <input v-model="rows" type="text" id="rows" />
            <input v-model="cols" type="text" id="cols" />
            <input v-model="bombCount" type="text" id="bomb-count" />
        </div>
        <button type="button" @click="startGame" class="submit-btn">
            Start
        </button>
    </div>
    <div class="label-container">
        <h2>{{ timerLabel }}</h2>
        <h2>{{ flagCount }} 🚩</h2>
    </div>

    <main>
        <MineGrid
            height="80vh"
            ref="minefield"
            @toggle-flag="handleToggleFlag"
            @finish-game="handleFinishGame"
        />
    </main>

    <!-- <div class="gameover-popup">
    <h2>{{ gameoverIsWin ? "You win!" : "You lose!" }}</h2>
  </div> -->

    <ModalPopup
        :show="showPopup"
        :win="isWin"
        :seconds="timerSeconds"
        :highscore="currentHighscore"
        @close="showPopup = false"
    />
</template>

<script lang="ts">
import moment from "moment";

export default {
    data() {
        return {
            rows: 16,
            cols: 16,
            bombCount: 40,
            flagCount: 20,
            timerSeconds: 0,
            timer: -1,
            isWin: false,
            showPopup: false,
            currentHighscore: NaN as number,
        };
    },
    methods: {
        handleToggleFlag(patch: any) {
            this.flagCount += patch.isFlagged ? -1 : +1;
        },

        handleFinishGame(isWin: boolean) {
            this.stopTimer();
            this.isWin = isWin;
            if (isWin) {
                let highscore = this.getHighScore(
                    this.rows,
                    this.cols,
                    this.bombCount
                );
                if (!highscore || highscore > this.timerSeconds) {
                    this.setHighScore(
                        this.rows,
                        this.cols,
                        this.bombCount,
                        this.timerSeconds
                    );
                }
            }
            this.currentHighscore = this.getHighScore(
                this.rows,
                this.cols,
                this.bombCount
            );
            this.showPopup = true;
        },

        startGame() {
            (this.$refs.minefield as any).setupGrid(
                this.rows,
                this.cols,
                this.bombCount
            );

            this.flagCount = parseInt(this.bombCount as any);
            this.startTimer();
        },

        startTimer() {
            this.timerSeconds = 0;
            clearInterval(this.timer);
            this.timer = setInterval(() => {
                this.timerSeconds++;
            }, 1000);
        },

        stopTimer() {
            clearInterval(this.timer);
        },

        setHighScore(
            rows: number,
            cols: number,
            bombCount: number,
            seconds: number
        ) {
            localStorage.setItem(
                JSON.stringify({
                    rows: rows,
                    cols: cols,
                    bombCount: bombCount,
                }),
                seconds.toString()
            );
        },

        //returns -1 if highscore is not set
        getHighScore(rows: number, cols: number, bombCount: number) {
            let highscore = localStorage.getItem(
                JSON.stringify({
                    rows: rows,
                    cols: cols,
                    bombCount: bombCount,
                })
            );
            return highscore ? parseInt(highscore) : NaN;
        },
    },
    computed: {
        timerLabel() {
            return moment()
                .startOf("hour")
                .second(this.timerSeconds)
                .format("mm:ss");
        },
    },
    mounted() {
        this.startGame();
    },
};
</script>

<style>
* {
    padding: 0;
    margin: 0;
}

#app {
    min-height: 100vh;
    width: 100vw;
    display: grid;
    grid-template-rows: 10vh 8vh 82vh;
    justify-items: center;
}

main {
    grid-row-start: 3;
}

.form-container {
    display: flex;
    align-items: flex-start;
}

.input-container {
    display: grid;
    grid-template-rows: 1fr 1fr;
    grid-template-columns: 1fr 1fr 1fr;
    gap: 0.3rem;
}

label {
    font-weight: bold;
    font-size: 1.2rem;
    margin-left: 0.2rem;
}

.submit-btn {
    display: inline;
    align-self: center;
    margin: 0.5rem;
    padding: 0.3rem;
}

.label-container {
    text-align: center;
}

.gameover-popup {
    position: fixed;
    left: 50%;
    top: 50%;
    transform: translate(-50%, -50%);
    background-color: antiquewhite;
}
</style>
