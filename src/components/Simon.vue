<template>
    <div class="container">
        <div class="field">
            <div
                :style="style1"
                class="box box1"
                v-on:click="litBox(1)"
                v-on:mousedown="createUserSequence(1)"
                v-bind:class="{ active: activeBoxes[0]}"
            ></div>
            <div
                :style="style2"
                class="box box2"
                v-on:click="litBox(2)"
                v-on:mousedown="createUserSequence(2)"
                v-bind:class="{ active: activeBoxes[1]}"
            ></div>
            <div
                :style="style3"
                class="box box3"
                v-on:click="litBox(3)"
                v-on:mousedown="createUserSequence(3)"
                v-bind:class="{ active: activeBoxes[2]}"
            ></div>
            <div
                :style="style4"
                class="box box4"
                v-on:click="litBox(4)"
                v-on:mousedown="createUserSequence(4)"
                v-bind:class="{ active: activeBoxes[3]}"
            ></div>
        </div>
        <div class="game">
            <div class="game__title">
                <h1>Simon the game</h1>
            </div>
            <div class="game__info">
                <h2>Раунд : {{step}}</h2>
                <div v-if="isPlay">
                    <button 
                        class="start" 
                        v-on:click="changeState"
                    >
                    Старт
                    </button>
                </div>
                <div v-else>
                    <button 
                        class="start" 
                        v-on:click="changeState"
                        disabled
                    >
                    Старт
                    </button>
                </div>
                <h2 class="warning">{{message}}</h2>
            </div>
            <div class="game-options">
                <h2>Сложность игры : {{gameMode}}</h2>
                <input type="radio" name="mode" value="Легко" v-model="gameMode">Легко
                <input type="radio" name="mode" value="Средне" v-model="gameMode">Средне
                <input type="radio" name="mode" value="Сложно" v-model="gameMode">Сложно
            </div>
        </div>
    </div>
</template>

<script>
const audio1 = new Audio('https://s3.amazonaws.com/freecodecamp/simonSound1.mp3');
const audio2 = new Audio('https://s3.amazonaws.com/freecodecamp/simonSound2.mp3');
const audio3 = new Audio('https://s3.amazonaws.com/freecodecamp/simonSound3.mp3');
const audio4 = new Audio('https://s3.amazonaws.com/freecodecamp/simonSound4.mp3');

export default {
    data() {   
        return {
            gameMode: 'Легко',
            sequence: [],
            userSequence: [],
            step: '_',
            message: '',
            activeBoxes: [false, false, false, false],
            state: 'off',
            opacity1: 0.5,
            opacity2: 0.5,
            opacity3: 0.5,
            opacity4: 0.5,
            isPlay: true
        }
    },
    computed: {
        style1: function() { return { opacity1: this.opacity1 } },
        style2: function() { return { opacity2: this.opacity2 } },
        style3: function() { return { opacity3: this.opacity3 } },
        style4: function() { return { opacity4: this.opacity4 } },
        
    },
    watch: {
        userSequence: function(newValue, oldValue) {
            if (newValue == oldValue) {
                if ((this.userSequence.length < this.sequence.length) && ((this.sequence.length > 0) && (this.userSequence.length > 0))) {
                    (this.isIdenticalSequences(this.sequence.slice(0, this.userSequence.length), this.userSequence)) ? null : this.resetGame()
                }
                if (this.sequence.length === this.userSequence.length) {
                    (this.isIdenticalSequences(this.sequence, this.userSequence) && ((this.sequence.length > 0) && (this.userSequence.length > 0))) ? this.nextRound() : this.resetGame()
                }
            }
        }
    },
    methods: {
        changeState: function() {
            if (this.state === 'off') {
                this.state = 'on'
                this.nextRound();
            } else {
                this.state = 'off'
            }
        },
        nextRound: function() {
            const currentBox = this.getRandomNumberOneToFour();
            this.sequence.push(currentBox);
            this.userSequence = [];
            this.step = this.sequence.length;
            this.playSequence();
            this.message = '';
            this.isPlay = false;
        },
        resetGame: function() {
            this.sequence = [];
            this.userSequence = [];
            this.message = `Вы проиграли на ${this.step} раунде`;
            this.state = 'off';
            this.isPlay = true;
            setTimeout((str = '_') => {this.step = str}, 10);
        },
        playSequence: function() {
            this.sequence.forEach( (elem, index) => {
                const interval = this.getInterval();
                setTimeout(this.litBox, interval * (index + 1), elem);
            })
        },
        createUserSequence: function(boxNum) {
            this.userSequence.push(boxNum);
        },
        isIdenticalSequences: function(arr1, arr2) {
            if (JSON.stringify(arr1) == JSON.stringify(arr2)) {
                return true;
            }

            return false;
        },
        getInterval: function() {
            if (this.gameMode === 'Легко') {
                return 1500;
            }
            if (this.gameMode === 'Средне') {
                return 1000;
            }
            if (this.gameMode === 'Сложно') {
                return 400;
            }
        },
        getRandomNumberOneToFour: () => Math.floor(Math.random() * 4 + 1),
        litBox: function(boxNum) {
            const self = this;
            switch (boxNum) {
                case 1:
                this.activeBoxes[0] = true;
                this.opacity1 = 1;
                audio1.play();
                setTimeout(function() {
                    self.activeBoxes[0] = false;
                    self.opacity1 = 0.5;
                }, 200);
                break;
                case 2:
                this.activeBoxes[1] = true;
                this.opacity2 = 1;
                audio2.play();
                setTimeout(function() {
                    self.activeBoxes[1] = false;
                    self.opacity2 = 0.5;
                }, 200);
                break;
                case 3:
                this.activeBoxes[2] = true;
                this.opacity3 = 1;
                audio3.play();
                setTimeout(function() {
                    self.activeBoxes[2] = false;
                    self.opacity3 = 0.5;
                }, 200);
                break;
                case 4:
                this.activeBoxes[3] = true;
                this.opacity4 = 1;
                audio4.play();
                setTimeout(function() {
                    self.activeBoxes[3] = false;
                    self.opacity4 = 0.5;
                }, 200);
                break;
            }
            return;
        },
    }
}
</script>

<style>
    div {
        margin: 0;
        padding: 0;
    }
    .container {
        display: flex;
        margin: 0 auto;
        width: 1000px;  
    }

    .field {
        display: flex;
        flex-wrap: wrap;

        width: 400px;
        height: 400px;
        border-radius: 50%;
        overflow: hidden;
        background: black;
    }

    .box {
        width: 198px;
        height: 198px;
        opacity: .5;
        cursor: pointer;
    }

    .game {
        width: 400px;
    }

    .game__title {
        text-transform: uppercase;
    }

    .box1 {
        background: blue;
    }

    .active {
        opacity: 1;
        border: solid 2px black;
    }

    .box2 {
        background: red;
    }

    .box3 {
        background: rgb(250, 204, 2);
    }

    .box4 {
        background: green;
    }

    .warning {
        color: red;
        text-transform: uppercase;
    }

    .start {
        text-transform: uppercase;
        width: 100px;
        height: 50px;
        margin: 0 auto;
        background: burlywood;
        cursor: pointer;
    }

</style>