<template>
  <div class="hello">
    <ul class="primary__numbers">
      <li v-for="item in primary" :key="item">{{item}}</li>
    </ul>
      <ul class="list">
        <item v-for="(item, index) in filterNumbers"
              :item="item"
              :key="time + index">
         {{index + 2}}
        </item>
      </ul>
      <button class="recalc" @click="recalculate">Calc</button>
  </div>
</template>

<script>
import * as dat from 'dat.gui';
import item from './item';


function RandomColor() {
  let color = null;
  const colors = ['#00d6c8', '#448AFF', '#F8BBD0', '#E91E63', '#C2185B', '#34f81b'];
  return () => {
    color = (color === null) ? 0 : (color + 1);
    return colors[color];
  }
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
  computed: {
    filterNumbers() {
      return this.numbers.filter((item, index) => index > 1)
    }
  },
  methods: {
    wait() {
      return new Promise((resolve) => {
        setTimeout(() => resolve(), this.speed);
      });
    },
    rend() {
      this.time = Date.now();
      let obj = JSON.stringify({cross: false, border: 'transparent', color: '#ffffff'});
      this.numbers = new Array(101).fill('').map(() => JSON.parse(obj));
      this.primary = [];
    },
    async recalculate() {
      if (this.status) {
        return
      }
      this.rend();
      this.status = true;
      const colorGen = new RandomColor();
      for (let i = 2; i < this.numbers.length; i++) {
        const color = colorGen();
        if (this.numbers[i].cross === false) {
          this.numbers[i].border = color;
          this.primary.push(i);
          for (let j = i * i; j < this.numbers.length; j += i) {
            if (this.numbers[j].cross === false) {
              await this.wait();
              this.numbers[j].cross = true;
              this.numbers[j].color = color;
            }
          }
        }
      }
      this.getPrime();
    },
    getPrime() {
      for (const [index, item] of this.numbers.entries()) {
        if (item.cross === false && index > 1) {
          item.color = '#b5ff38';
          item.cross = true;
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
  * {
    box-sizing: border-box;
    font-family: 'Bebas Neue', cursive;
  }
 .list {
   display: flex;
   list-style: none;
   padding-left: 0;
   margin: 0;
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
    font-weight: 400;
    span {
      padding: 5px;
      line-height: 1;
      border-radius: 50%;
      border-width: 3px;
      border-style: solid;
      width: 40px;
      height: 40px;
      display: flex;
      justify-content: center;
      align-items: center;
    }
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
      /*color: #b5ff38;*/
    }
  }
</style>
