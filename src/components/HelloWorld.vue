<template>
  <div class="hello">
    <ul class="primary__numbers">
      <li v-for="item in primary" :key="item">{{item}}</li>
    </ul>
      <ul class="list">
        <item v-for="(item, index) in numbers" :item="item" :key="time + index">
          <span>{{index}}</span>
        </item>
      </ul>
      <button class="recalc" @click="recalculate">Calc</button>
  </div>
</template>

<script>
import * as dat from 'dat.gui';
import item from './item';
const colors = ['#BDBDBD', '#448AFF', '#F8BBD0', '#E91E63', '#C2185B'];
function randomColor() {
  let index = Math.floor(Math.random() * colors.length);
  return colors[index];
}

export default {
  name: 'HelloWorld',
  data() {
    return {
      numbers: [],
      speed: 100,
      time: Date.now(),
      primary: [],
      status: false,
    };
  },
  methods: {
    wait() {
      return new Promise((resolve) => {
        setTimeout(() => resolve(), this.speed);
      });
    },
    rend() {
      this.time = Date.now();
      let obj = JSON.stringify({cross: false, color: '#ffffff'});
      this.numbers = new Array(101).fill('').map(() => JSON.parse(obj));
      this.primary = [];
    },
    async recalculate() {
      if (this.status) {
        return
      }
      this.rend();
      this.status = true;
      for (let i = 2; i < this.numbers.length; i++) {
        let color = randomColor();
        if (this.numbers[i].cross === false) {
          for (let j = i * i; j < this.numbers.length; j += i) {
            await this.wait();
            this.numbers[j].cross = true;
            this.numbers[j].color = color;
          }
        }
      }
      this.getPrime();
    },
    getPrime() {
      for (const [index, item] of this.numbers.entries()) {
        if (item.cross === false && index > 1) {
          item.color = 'green';
          item.cross = true;
          this.primary.push(index);
        }
      }
      this.status = false;
    }
  },
  components: {
    item
  },
  mounted() {
    this.rend();
    this.gui = new dat.GUI();
    this.gui.add(this, 'speed', 100, 5000);

  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="scss">
 .list {
   display: flex;
   list-style: none;
   padding-left: 0;
   margin-top: 0;
   width: 100vw;
   flex-wrap: wrap;
   justify-content: center;
   align-items: center;
   align-content: center;
   min-height: 100vh;
 }
  .item {
    width: 5vw;
    height: 5vw;
    border: 1px solid grey;
    display: flex;
    justify-content: center;
    align-items: center;
  }
  body {
    padding: 0;
    margin: 0;
    overflow: hidden;
  }
  html {
    padding: 0;
    margin: 0;
  }
  .recalc {
    position: fixed;
    top: 10px;
    left: 40px;
  }

  .primary__numbers {
    position: absolute;
    right: 20px;
    top: 40px;
    display: flex;
    list-style: none;
    li {
      margin-right: 5px;
    }
  }
</style>
