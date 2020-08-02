<template>
  <div class="container">
    <h1>Simon Says</h1>
    <div class="simon">
      <ul>
        <li class="red" data-tile="1" @click="clickBtn"></li>
        <li class="blue" data-tile="2" @click="clickBtn"></li>
        <li class="yellow" data-tile="3" @click="clickBtn"></li>
        <li class="green" data-tile="4" @click="clickBtn"></li>
      </ul>
    </div>
    <div class="game-info">
      <h2>
        Раунд:
        <span data-round="0">0</span>
      </h2>
      <button data-action="start" @click="start">Запустить</button>
      <p data-action="lose">
        Пройдено
        <span data-round="0"></span> раундов!
      </p>
    </div>
    <div class="game-options">
      <h2>Уровни сложности:</h2>
      <input type="radio" name="mode" value="1.5" checked />Легкий
      <br />
      <input type="radio" name="mode" value="1" />Средний
      <br />
      <input type="radio" name="mode" value="0.4" />Сложный
    </div>
  </div>
</template>

<script>
export default {
  name: "Game",
  data: () => ({
    render: undefined,
    round: 0,
    speed: 0,
    array: [],
    indexNow: -1,
    status: false
  }),
  methods: {
    clickBtn(event) {
      const target = event.target,
        number = target.getAttribute("data-tile"),
        audio = new Audio(require(`@/sounds/${number}.ogg`));

      target.classList.add("active");
      audio.play();

      if (this.status) {
        if (this.indexNow !== -1) {
          if (this.array[this.indexNow] !== +number) {
            this.status = false;
            this.reset();
          } else if (this.array.length - 1 === this.indexNow) {
            setTimeout(this.startGame, 1000);
          } else {
            this.indexNow++;
          }
        }
      }

      setTimeout(() => {
        target.classList.remove("active");
      }, 150);
    },
    start() {
      this.reset();
      const options = document.querySelectorAll("[name='mode']");
      let speed = 0;
      options.forEach(element => {
        if (element.checked) {
          speed = +element.value;
        }
      });

      if (speed !== 0) {
        this.speed = speed * 1000;
        this.startGame();
      }
    },
    reset() {
      clearTimeout(this.render);
      this.indexNow = -1;
      this.round = 0;
      this.array = [];
      this.setRound(this.round);
    },
    randomNum() {
      return Math.floor(Math.random() * 4 + 1);
    },
    setRound(number) {
      const attrRound = document.querySelector("[data-round]");
      attrRound.setAttribute("data-round", number);
      attrRound.textContent = number;
    },
    startGame() {
      this.status = true;
      this.round++;

      clearTimeout(this.render);
      this.setRound(this.round);

      let i = 0;
      this.array.push(this.randomNum());
      this.indexNow = 0;

      const animation = function() {
        if (this.array.length > i) {
          const number = this.array[i],
            element = document.querySelector(`[data-tile="${number}"]`),
            audio = new Audio(require(`@/sounds/${number}.ogg`));

          element.classList.add("active");
          audio.play();

          setTimeout(() => {
            element.classList.remove("active");
          }, 250);

          i++;
          this.render = setTimeout(animation.bind(this), this.speed);
        }
      };
      animation.apply(this);
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
body {
  font-family: Arial, serif;
  color: #333;
  -webkit-user-select: none; /* Chrome/Safari */
  -moz-user-select: none; /* Firefox */
  -ms-user-select: none; /* IE10+ */
  -o-user-select: none;
  user-select: none;
}

h1 {
  margin: 1em 0 2em;
  text-align: center;
}

ul {
  list-style: none;
}

ul,
li {
  padding: 0;
  margin: 0;
}

p[data-action="lose"] {
  display: none;
}

.active {
  opacity: 1 !important;
  border: 2px solid black;
}

.clearfix {
  width: 100%;
  clear: both;
}

.container {
  width: 540px;
  margin: 0 auto;
}

.simon {
  background: #fff;
  position: relative;
  float: left;
  margin-right: 3em;
  width: 302px;
  height: 295px;
  -webkit-border-radius: 150px 150px 150px 150px;
  border-radius: 150px 150px 150px 150px;
  -moz-box-shadow: 2px 1px 12px #aaa;
  -webkit-box-shadow: 2px 1px 12px #aaa;
  -o-box-shadow: 2px 1px 12px #aaa;
  box-shadow: 2px 1px 12px #aaa;
}

.red,
.blue,
.yellow,
.green {
  opacity: 0.6;
  height: 290px;
  -webkit-border-radius: 150px 150px 150px 150px;
  border-radius: 150px 150px 150px 150px;
  position: absolute;
  text-indent: 10000px;
}

.red:hover,
.blue:hover,
.yellow:hover,
.green:hover {
  border: 2px solid black;
}

.red {
  background: #ff5643;
  clip: rect(0px, 300px, 150px, 150px);
  width: 296px;
}

.blue {
  background: dodgerblue;
  clip: rect(0px, 150px, 150px, 0px);
  width: 300px;
}

.yellow {
  background: #feef33;
  clip: rect(150px, 150px, 300px, 0px);
  width: 300px;
}

.green {
  background: #bede15;
  clip: rect(150px, 300px, 300px, 150px);
  width: 296px;
}

.game-info {
  margin-top: 90px;
}

.game-info button {
  width: 5.8em;
  box-sizing: border-box;
  font-size: 1.4em;
  -webkit-border-radius: 10px 10px 10px 10px;
  border-radius: 10px 10px 10px 10px;
  background: #6dabe8;
  border: none;
  padding: 0.3em 0.6em;
}

.game-info button:hover {
  background: #78bcff;
}

.game-options h2 {
  margin-top: 30px;
  margin-bottom: 0;
}

.game-options input[type="radio"] {
  margin-right: 10px;
}

.hoverable:hover {
  cursor: pointer;
}

footer {
  position: absolute;
  bottom: 20px;
  width: 100%;
  clear: both;
  text-align: center;
}
</style>
