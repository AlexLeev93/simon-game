<template>
    <div>
        <h1>Simon Says</h1>
        <div style="display: flex; justify-content: space-evenly">
            <div>
                <div>
                <div class="panel top_left_panel" v-on:click="clickedPanel(1)" v-bind:class="{ active: isPanelOneActive}">
                    <audio id="audio1">
                        <source src="https://s3.amazonaws.com/freecodecamp/simonSound1.mp3" type="audio/ogg">
                    </audio>
                </div>
                <div class="panel top_right_panel" v-on:click="clickedPanel(2)" v-bind:class="{ active: isPanelTwoActive}">
                    <audio id="audio2">
                        <source src="https://s3.amazonaws.com/freecodecamp/simonSound2.mp3" type="audio/ogg">
                    </audio>
                </div>
            </div>
            <div>
                <div class="panel bottom_left_panel" v-on:click="clickedPanel(3)" v-bind:class="{ active: isPanelThreeActive}"> 
                    <audio id="audio3">
                        <source src="https://s3.amazonaws.com/freecodecamp/simonSound3.mp3" type="audio/ogg">
                    </audio>
                </div>
                <div class="panel bottom_right_panel" v-on:click="clickedPanel(4)" v-bind:class="{ active: isPanelFourActive}">
                    <audio id="audio4">
                        <source src="https://s3.amazonaws.com/freecodecamp/simonSound4.mp3" type="audio/ogg">
                    </audio>
                </div>
            </div>
            </div>
            <div>
                <h2>Round: {{step}}</h2>
                <div style="display: flex; flex-direction: column; align-items: center">
                  <div class="button" v-on:click="changeState()">{{state}}</div>
                  <div class="info-box" v-if="message">Вы не угадали после раунда {{score}}</div>
                </div>
                <h3>Game options</h3>
                <div class="game-options">
                  <div class="game-options-item">
                    <div><input v-model="dificulty" type="radio" value="easy"></div>
                    <div>Легкий</div>
                  </div>
                  <div class="game-options-item">
                    <div><input v-model="dificulty" type="radio" value="normal"></div>
                    <div>Средний</div>
                  </div>
                  <div class="game-options-item">
                    <div><input v-model="dificulty" type="radio" value="hard"></div>
                    <div>Сложный</div>
                  </div>
                </div>
            </div>
        </div>

        </div>
</template>

<script>
export default {
    data()  {
        return{
            step: 0,
            userTurn: false,
            state: 'Start',
            isPlaying: false,
            pattern: [],
            index: 0,
            isPanelOneActive: false,
            isPanelTwoActive: false,
            isPanelThreeActive: false,
            isPanelFourActive: false,
            message: false,
            score: 0,
            dificulty: 'easy',
            time: 1500
        }
    },
  methods: {
    changeState: function() {
      if(this.dificulty === 'normal'){
          this.time = 1000
          console.log('normal')
        }else if(this.dificulty === 'hard'){
          this.time = 400
          console.log('hard')
        }
      if (this.state === 'Start') {
        this.state = 'Stop';
        this.message = null;
        this.isPlaying = true;
        this.step = 0;
        this.score = 0;
        this.computerTurn();
      } else {
        this.resetGame();
      }
      
    },
    computerTurn: function() {
      let self = this;
      this.index = 0;
      this.step++;
      this.pattern.push(this.getRandomNumberOneToFour());
      this.showPattern(function(){
        self.userTurn = true;
      });
    },
    clickedPanel: function(panelNum) {
      let self = this;
      if (this.state === 'Start') {
        return;
      }
      this.clickEffect(panelNum);
      let isCorrect = this.checkPattern(panelNum);
      if (!isCorrect) {
        
          self.processGameOver();
          
      } else {
        if (this.index === this.pattern.length - 1) {
          this.score++;
          setTimeout(function() {
             self.userTurn = false;
            self.computerTurn();
          }, this.time);
        } else {
          this.index++;
        }
      }
    },
    processGameOver: function(){
          this.message = true
          this.resetGame();
    },
    getRandomNumberOneToFour: function() {
      return Math.floor(Math.random() * 4 + 1);
    },
    checkPattern: function(panelNum) {
      return (this.pattern[this.index] === panelNum);
    },
    resetGame: function() {
      this.step = 0;
      this.userTurn = false;
      this.state = 'Start';
      this.isPlaying = false;
      this.pattern = [];
      this.index = 0;
    },
    showPattern: function(callback) {
      let self = this
      let i=0;
      timer = setInterval(function() {
        if(i >= self.pattern.length){
          self.stopInterval();
        }
        self.clickEffect(self.pattern[i]);
        i++;
      }, this.time);
      callback();
    },
    stopInterval: function(){
      clearInterval(this.timer);
    },
    clickEffect: function(panelNum) {
      let self = this
      switch (panelNum) {
        case 1:
          this.isPanelOneActive = true;
          audio1.play();
          setTimeout(function() {
            self.isPanelOneActive = false;
          }, 100);
          break;
        case 2:
          this.isPanelTwoActive = true;
          audio2.play();
          setTimeout(function() {
            self.isPanelTwoActive = false;
          }, 100);
          break;
        case 3:
          this.isPanelThreeActive = true;
          audio3.play();
          setTimeout(function() {
            self.isPanelThreeActive = false;
          },100);
          break;
        case 4:
          this.isPanelFourActive = true;
          audio4.play();
          setTimeout(function() {
            self.isPanelFourActive = false;
          }, 100);
          break;
      }
      return;
    }
  }
}
</script>

<style scoped>

.game-options-item{
  display: flex;
}

.button {
    font-size: 1.4em;
    border-radius: 10px 10px 10px 10px;
    background: #6DABE8;
    border: none;
    padding: 0.3em 0.6em;
    width: 85px;
}

.info-box{
  margin-top: 30px;
  text-align: center;
  color: black;
  font-weight: 400;
  font-size: 18px;
}

h1 {
  text-align: center;
  font-size: 64px;
  font-weight: 900;
  font-family: sans-serif;
  color: #323232;
}

.panel {
    width: 150px;
    opacity: 0.6;
    height: 150px;
    display: inline-block;
    cursor: pointer;
}

.panel:hover {
    box-shadow: inset 0 0 0 3px white;
    box-sizing: border-box;
}

.bottom_left_panel {
    background-color: #FEEF33;
    border-bottom-left-radius: 100%;
    clip: rect(150px, 150px, 300px, 0px);
    
}
.bottom_right_panel {
    background-color: #BEDE15;
    border-bottom-right-radius: 100%;
    clip: rect(150px, 150px, 300px, 0px);
}
.top_left_panel{
    background-color: dodgerblue;
    border-top-left-radius: 100%;
    clip: rect(150px, 150px, 300px, 0px);
}
.top_right_panel{
    background-color: #FF5643;
    border-top-right-radius: 100%;
    clip: rect(150px, 150px, 300px, 0px);
}
.active {
    background-color: hsl(50, 50%, 10%);
    border: 1px solid black;
}
</style>