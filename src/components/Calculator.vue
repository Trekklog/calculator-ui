<template>
  <div class="center">
    <div id="layout">

      <div class="dots">
        <span class="dot"></span>
        <span class="dot"></span>
        <span class="dot"></span>
      </div>

      <div class="display">
        <p class="track">{{ display_number || ' '}}</p>
        <h1 ref='value' class="result">{{ number_track || track || 0 }}</h1>
      </div>

      <div class="operators">
        <div @click=clean() class="keys">C</div>
        <div @click="latest_operation('plus_or_minus')" class="keys">+/-</div>
        <div @click="latest_operation('percent')" class="keys">%</div>
        <div @click="latest_operation('division')" class="keys">/</div>
        <div @click=value(7) class="keys">7</div>
        <div @click=value(8) class="keys">8</div>
        <div @click=value(9) class="keys">9</div>
        <div @click="latest_operation('multiplication')" class="keys">x</div>
        <div @click=value(4) class="keys">4</div>
        <div @click=value(5) class="keys">5</div>
        <div @click=value(6) class="keys">6</div>
        <div @click="latest_operation('subtraction')" class="keys">-</div>
        <div @click=value(1) class="keys">1</div>
        <div @click=value(2) class="keys">2</div>
        <div @click=value(3) class="keys">3</div>
        <div @click="latest_operation('addition')" class="keys">+</div>
        <div @click=value(0) class="keys">0</div>
        <div @click="latest_operation('comma')" class="keys comma">,</div>
        <div @click="get_value()" class="keys equal">=</div>
      </div>
    </div>

  </div>
</template>

<script>
  import math from 'mathjs'

  export default {
    name: 'Calculator',
    data() {
      return {
        display_number: '',
        track: '', // result value
        number_track: ''
      }
    },

    methods: {
      value(val) {

        if (this.number_track.slice(0, 1).includes('0') && val === 0) {
          if (!this.number_track.includes('0.')) {
            return
          }
        }

        if (this.display_number.slice(-1).includes(')')) {
          return
        }

        if (this.track) {
          this.track = ''
          this.display_number = ''
          this.number_track = ''
        }

        if (this.check_operation()) {
          this.track = ''
          this.number_track = ''
        }

        if (this.number_track.split('').length >= 8) {
          return
        }

        this.display_number += val
        this.number_track += val

      },

      check_operation() {
        let last_index_of_val = this.display_number.slice(-1)

        if (['+', '-', '*', '/'].indexOf(last_index_of_val) >= 0) {
          return true
        }

      },

      check_and_replace() {
        let track_number_value = -this.number_track.split('').length
        this.display_number = this.display_number.slice(0, track_number_value)
      },

      get_value() {

        if (this.check_operation()) {
          this.display_number = this.display_number.slice(0, -1)
        }

        let sum = math.eval(this.display_number)
        this.track = sum
        this.number_track = ''

      },

      latest_operation(operation) {

        if (this.check_operation()) {
          this.display_number = this.display_number.slice(0, -1)
        }

        switch (operation) {
          case 'addition':
            this.display_number += '+'
            break

          case 'subtraction':
            this.display_number += '-'
            break

          case 'multiplication':
            this.display_number += '*'
            break

          case 'division':
            this.display_number += '/'
            break

          case 'percent':
            this.check_and_replace()

            let divide_number = `${parseFloat(this.number_track) / 100}`

            this.display_number += `(${divide_number})`
            this.number_track = divide_number

            break

          case 'plus_or_minus':
            this.check_and_replace()

            if (!this.number_track.includes('-')) {
              this.number_track = '-'.concat(this.number_track)
              this.display_number += this.number_track
            } else {
              this.number_track = this.number_track.slice(1)
              this.display_number += this.number_track
            }
            break

          case 'comma':
            if (this.number_track.includes('.')) {
              return
            }

            this.display_number += '.'
            this.number_track += '.'

            break
        }

      },

      clean() {
        this.display_number = ''
        this.number_track = ''
        this.track = ''
      }
    }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
  #layout {
    grid-column: 3/4;
    display: grid;
    padding: 1rem 0px;
    justify-content: center;
    border-radius: 1.5rem;
    box-shadow: 0px 0px 20px 4px rgba(0, 0, 0, 0.2);
  }

  body {
    background: #F7F7F7;
    font-family: 'Roboto', sans-serif;
    font-weight: 300;
    box-sizing: border-box;
    margin-top: 3rem;
  }

  .justify {
    width: 70vw;
    margin: 0 auto;
  }

  .dots {
    display: flex;
  }

  .dot {
    height: 25px;
    width: 25px;
    background-color: #A6A5A4;
    border-radius: 50%;
    margin-left: 5px;
  }

  .display {
    display: grid;
    grid-gap: 1.8rem;
    text-align: end;
    margin-bottom: 2.5rem;
  }

  .track {
    color: #a6a5a4;
    min-height: 1.8rem;
    font-size: 1.5rem;
    margin-right: 0.2rem;
  }

  .result {
    color: #444444;
    font-size: 4rem;
    letter-spacing: 0.2rem;
  }

  .operators {
    display: grid;
    color: #444444;
    text-align: center;
    align-items: center;
    grid-gap: 2rem;
    grid-template-columns: repeat(4, 1fr);
    font-size: 4rem;
    cursor: pointer;
  }

  .keys {
    cursor: pointer;
    transition: color 0.1s ease-in-out;
  }

  .keys:hover {
    color: #f16464;
  }

  .equal {
    background: #ffab91;
    line-height: 60px;
    height: 60px;
    width: 60px;
    justify-self: center;
    border-radius: 50%;
    grid-column: 4/5;
    grid-row: 5;
  }

  .equal:hover {
    color: #1e3242;
  }

  .comma {
    grid-column: 3/4;
    grid-row: 5;
  }

  .center {
    display: grid;
    grid-template-columns: 1fr 1fr minmax(250px, 450px) 1fr 1fr;
  }
</style>