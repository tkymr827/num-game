<template>
    <div id="app">
    <div class="game">
        <div class="reload" @click="reload"><i class="fas fa-redo-alt"></i>やり直す</div>
        <div class="timer" :class="clear">{{sec}}.{{timer}}秒</div>
        <div class="content">
            <ul>
                <li v-for="button in buttons" @click="click(value[button-1])" :class="{'active':value[button-1]==check}" :key="button">{{value[button-1]}}</li>
            </ul>
        </div>
        <div class="start" @click.once="start">START</div>
    </div>
    <transition>
        <div class="overlay" v-if="modal">
            <div class="inner">
                <h1>記録:<span>{{sec}}.{{timer}}</span>秒</h1>
                <p>名前を入れてランキング登録</p>
                <input type="text" v-model="name" />
                <button @click="submit">登録</button>
                <div class="close" @click="close" v-if="modal">Close</div>
            </div>
        </div>
    </transition>
    <div class="rule">
        <h2>ルール</h2>
        <p>数字を順番に押すだけ</p>
    </div>
    <div class="ranking">
        <h2>ランキング</h2>
        <table border="1">
            <tr>
                <th>Ranking</th>
                <th>Name</th>
                <th>Time</th>
            </tr>
            <!-- <tr>
                <th>{{rank}}</th>
                <th>{{name}}</th>
                <th>{{time}}</th>
            </tr> -->
        </table>
    </div>
</div>
</template>

<style lang="scss">
* {
    padding: 0;
    margin: 0;
    box-sizing: border-box;
    user-select: none; //クリックしても選択されないようになる
}

html {
    font-size: 62.5%;
}

#app {
    background: rgba(250, 226, 162, 0.877);
    display: flex;
    justify-content: center;
    align-items: center;
    min-height: 100vh;
    position: relative;

    .game {
        height: 60rem;
        width: 50rem;
        background: #fff;
        border-radius: 5rem;
        display: inline-block;
        .reload {
            float: left;
            text-align: left;
            margin: 4rem 0 2rem 4rem;
            font-size: 3rem;
            cursor: pointer;
        }
        .timer {
            float: right;
            text-align: right;
            margin: 4rem 4rem 2rem 0;
            font-size: 3rem;
            color: #555;
        }
        .clear {
            color: red;
            font-weight: bold;
            font-style: italic;
        }

        .content {
            clear: both;
            // background: red;
            height: 40rem;
            ul {
                display: flex;
                justify-content: space-evenly;
                flex-wrap: wrap;
                .active {
                    background: red;
                }
                li {
                    list-style: none;
                    height: 10rem;
                    width: 10rem;
                    background: rgba(250, 226, 162, 0.877);
                    margin: 1.4rem;
                    line-height: 10rem;
                    font-size: 2.5rem;
                    text-align: center;
                    border-radius: 2rem;
                    box-shadow: 0 0.5rem;
                    cursor: pointer;
                    &:active {
                        position: relative;
                        top: 0.5rem;
                        box-shadow: none;
                    }
                }
            }
        }
        .start {
            text-align: center;
            margin: 2rem auto;
            padding-left: 1rem;
            letter-spacing: 1.5rem;
            background: red;
            color: #fff;
            height: 5rem;
            width: 20rem;
            border-radius: 5rem;
            line-height: 5rem;
            font-weight: bold;
            font-size: 2rem;
            box-shadow: 0 0.5rem #555;
            cursor: pointer;

            &:active {
                position: relative;
                top: 0.5rem;
                box-shadow: none;
            }
        }
    }
    .rule {
        position: absolute;
        left: 20rem;
        top: 20rem;
        font-size: 2rem;
    }
    .ranking {
        position: absolute;
        right: 20rem;
        top: 20rem;
        font-size: 2rem;
    }

    .overlay {
        position: fixed;
        top: 0;
        left: 0;
        height: 100%;
        width: 100%;
        background: rgba(0, 0, 0, 0.6);
        z-index: 1;
        display: flex;
        justify-content: center;
        align-items: center;
        .inner {
            z-index: 2;
            height: 60rem;
            width: 90rem;
            background: beige;
            position: relative;

            display: flex;
            justify-content: center;
            align-items: center;
            flex-direction: column;
            font-size: 3rem;
            span {
                font-size: 8rem;
                font-weight: bold;
                color: navy;
            }
            .close {
                position: absolute;
                bottom: 2rem;
                right: 2rem;
                font-size: 2rem;
                background: #2e2e2e;
                color: rgb(250, 175, 35);
                padding: 1rem 3rem;
                border-radius: 20%;
                cursor: pointer;
                z-index: 3;
            }
        }
    }
    .v-enter {
        opacity: 0;
    }
    .v-enter-active {
        transition: opacity 2s;
    }
}

</style>

<script>
import axios from 'axios'
export default {
  data: function () {
    return {
      timer: 0,
      sec: 0,
      buttons: 0,
      value: [1, 2, 3, 4, 5, 6, 7, 8, 9],
      check: 1,
      clear: '',
      modal: false,
      name: '',
      cleartime: '',
      starttimer: ''
    }
  },
  methods: {
    start: function () {
      this.starttimer = setInterval(() => {
        this.timer++
        if (this.timer === 10) {
          this.sec++
          this.timer = 0
        }
      }, 100)

      this.buttons = 9

      for (let i = this.value.length - 1; i > 0; i--) {
        let r = Math.floor(Math.random() * (i + 1))
        let tmp = this.value[i]
        this.value[i] = this.value[r]
        this.value[r] = tmp
      }
    },
    click: function (num) {
      if (num === this.check) {
        this.check++
        console.log(num)
        if (this.check === this.value.length + 1) {
          clearInterval(this.starttimer)
          this.clear = 'clear'
          this.modal = !this.modal
          let strsec = String(this.sec)
          let strtimer = String(this.timer)
          this.cleartime = strsec + '.' + strtimer
          console.log(this.cleartime)
        }
      }
    },
    close: function () {
      this.modal = !this.modal
    },
    reload: function () {
      location.reload()
    },
    submit: function () {
      console.log(this.name)
      console.log(this.cleartime)
      axios.post('https://firestore.googleapis.com/v1/projects/num-game-ranking/databases/(default)/documents/rank'
        , {
          fields: {
            name: {
              stringValue: this.name
            },
            cleartime: {
              stringValue: this.cleartime
            }
          }
        }
      )
    }
  }
}
</script>
