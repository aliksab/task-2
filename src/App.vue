<template>
  <div id="#app">
   <div class="container">
      <div class="seetings">
        <div>
          <div class="output">
            <h3>{{output}}</h3>
          </div>
        </div>
        <div>
          <div class="btns">
            <button @click="start" :disabled="started==true" class="start">Старт</button>
            <button :disabled="canPress ===false" @click="reset" class="reset">Заново</button>
          </div>
          <div class="change-level">
           
            <label for="easy-level">Легкий</label>
            <input value="1500" name="checkLevel" v-model="levelValue" id="easy-level" type="radio" />
            <label for="medium-level">Средний</label>
            <input value="1000" name="checkLevel" v-model="levelValue" id="medium-level" type="radio"/>
            <label for="hard-level">Тяжелый</label>
            <input value="400" name="checkLevel" v-model="levelValue" id="hard-level" type="radio" />
          </div>
        </div>
      </div>
      <div class="game">
        <div>
          <button
            id="red"
            type="button"
            class="mybutton"
            :disabled="canPress===false"
            @click="addInput(1)"
          ></button>
          <button
            id="blue"
            type="button"
            class="mybutton"
            :disabled="canPress===false"
            @click="addInput(2)"
          ></button>
        </div>
        <div>
          <button
            id="green"
            type="button"
            class="mybutton"
            :disabled="canPress===false"
            @click="addInput(3)"
          ></button>
          <button
            id="yellow"
            type="button"
            class="mybutton"
            :disabled="canPress===false"
            @click="addInput(4)"
          ></button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "App",
  components: {},
  data() {
    return {
      strict: false,
      started: false,
      canPress: false,
      sequence: "",
      input: "",
      inputTurn: 0,
      output: "Simon the game",
      turn: 0,
      maxTurn: 20,
      levelValue: 1500,
      CIRCLES_PARTS: {
        1: "red",
        2: "blue",
        3: "green",
        4: "yellow",
      },
      SONG: {
        1: require('@/assets/track1.mp3'),
        2: require('@/assets/track2.mp3'),
        3: require('@/assets/track3.mp3'),
        4: require('@/assets/track0.mp3')
      },
    };
  },
  methods: {
    start() {
      this.reset();
      this.started = true;
      this._turn();
    },
    _turn() {
      if (this.turn === this.maxTurn) {
        this.output = "Ты победил!";
        this.started = false;
        this.canPress = false;
        setTimeout(this.reset, 3000);
      } else {
        this.canPress = false;
        this.output = "Повторов: " + ++this.turn;
        this.sequence += Math.floor(Math.random() * 4 + 1);
        this.play();
      }
    },
    play() {
      let delayBase = 100;
      let baseDuration = +this.levelValue;
      for (let i = 0; i < this.sequence.length; i++) {
        let bt = document.getElementById(this.CIRCLES_PARTS[this.sequence.charAt(i)]);
        let audio = new Audio(this.SONG[this.sequence.charAt(i)]);
        this.flash(bt, audio, delayBase, baseDuration);
        delayBase += baseDuration;
      }
      setInterval(this.getInput, delayBase);
    },
    flash(element, audio, delay, flashDuration) {
      setTimeout(function () {
        element.classList.add("btActive");
        audio.play();
      }, delay);
      setTimeout(function () {
        element.classList.remove("btActive");
      }, delay + flashDuration - 100);
    },
    getInput() {
      this.canPress = true;
    },
    addInput (bt) {
      if (this.canPress === true) {
        this.input = this.input + bt;
        let beep = new Audio(this.SONG[bt]);
        beep.play();
        this.check();
      }
    },
    check() {
      this.canPress = false;
      let turn = this.inputTurn;
      if (this.input.charAt(turn) === this.sequence.charAt(turn)) {
        this.inputTurn++;
        if (this.inputTurn === this.sequence.length) {
          this.input = "";
          this.inputTurn = 0;
          setTimeout(this._turn, 2000);
        }
      } else {
        if (this.strict) {
          this.output = "Ты проиграл!";
          setTimeout(this.reset, 5000);
        } else {
          this.input = "";
          this.inputTurn = 0;
          this.output = "Ты промазал! Повтори!";
          setTimeout(this.play, 2000);
        }
      }
    },
    reset() {
      this.sequence = "";
      this.input = "";
      this.inputTurn = 0;
      this.turn = 0;
      this.started = false;
      this.output = "Simon the game";
      this.canPress = false;
    },
  },
};
</script>

<style lang="scss">
$red: #ff1900;
$green: #00ff2a;
$yellow: #ffee00;
$blue: #0080ff;


.header h1 {
  font-family: 'Arial', sans-serif;
  color: teal;
  font-size: 70px;
}

.container {
  width: 300px;
  margin: 0 auto;
}

input {
  margin-top: 10px;
  margin-right: 10px;
  margin-left: 6px;
}

.seetings {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}

.btns{ 
  text-align: center;
  margin-bottom: 10px;
}

.change-level {
  margin-bottom: 10px;
}

.start .reset {
  margin-right: 10px;
  padding: 5px 20px;
  border: none;
  opacity: 0.9;
  color: white;
  border-radius: 5px;
  font-size: 16px;
}
.start {
  background: green;
  height: 30px;
  width: 70px;
  border-radius: 15%;
}

.reset {
  background: rgb(230, 169, 58);
  height: 30px;
  width: 70px;
  border-radius: 15%;
}

.output {
  text-align: center;
}

button {
  outline: none;
  border: none;
}

#red, #green, #blue, #yellow {
  opacity: 0.8;
  height: 290px;
  border-radius: 150px 150px 150px 150px;
  position: absolute;
}

#red {
  background: $red;
  clip: rect(0px, 300px, 150px, 150px);
  width: 296px;
}

#green{
  background: $green;
  clip: rect(150px, 300px, 300px, 150px);
  width: 296px;
}

#yellow{
  background: $yellow;
  clip: rect(150px, 150px, 300px, 0px);
  width: 300px;
}

#blue {
  background: $blue;
  clip: rect(0px, 150px, 150px, 0px);
  width: 300px;
}

.output {
  font-family: "Bungee";
}

.mybutton {
  height: 120px;
  width: 120px;
  margin: 3px;
  outline: none;
  cursor: pointer;
}

.mybutton:active {
  border: 10px;
  transform: translateY(-4px);
  box-shadow: 0 5px #999;
  transition: all 0.1s linear;
  opacity: 1;
}

#red:active {
  background: darken($red, 15%);
}

#green:active {
  background: darken($green, 15%);
}

#yellow:active {
  background: darken($yellow, 15%);
}

#blue:active {
  background: darken($blue, 15%);
}

.mybutton:disabled {
  opacity: 0.3;
}

.start:disabled, .reset:disabled {
  opacity: .5;
}

.btActive {
  border: 10px;
  transform: translateY(-4px);
  box-shadow: 0 5px #999;     
}       
</style>

