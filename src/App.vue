
<template>
  <div class="main">

    <h1 v-show="!winner"> {{ turn }}</h1>
    <h1 id="winner" v-show="winner"> Player{{ winner }} victory!</h1>
    <button v-show="winner" style="margin:10px;" @click="reset">Play again?</button>
    <div class="row" v-for="row, rowIndex in map">
      <div class="col" v-for="col, index, in row" :id="`${index}-${rowIndex}`" :key="col" :value="col" @click="playCoin(index, rowIndex)" >
        <div class="square">
          <div class="round-slot"></div>
        </div>
      </div>
    </div>
  </div>
</template>


<script lang="ts">



export default {
  data () {
    return {
      map: [],
      turn: 'Player1',
      winner: null,
    }
  },
  mounted() {
    this.initMap(6,7)
    console.log(this.map)
  },
  methods: {
    initMap(y:number,x:number):void {
      for (let i = 0; i < y;i++) {
        this.map.push([])
        for (let j = 0; j < x; j++) {
          this.map[i].push('x')
        }
      }
    },
    reset() {
      this.map= []
      this.initMap(6, 7);
      this.turn = 'Player1';
      this.winner = null;
    },
    playCoin(col: number): void {
      if (this.winner) {
        return
      }
      let new_row = this.map.length - 1;
      while (new_row >= 0 && this.map[new_row][col] !== 'x') {
        new_row--;
      }
      
      if (new_row >= 0) {
        if (this.turn === 'Player1') {
          this.map[new_row][col] = '1';
        } else {
          this.map[new_row][col] = '2';
        }
        this.$nextTick(() => {
          const id = col + "-" + new_row;
          const slot = document.getElementById(id);
          if (slot) {
            if (this.turn === 'Player1') {
              slot.lastChild.lastChild.style.backgroundColor = "red";
              this.turn = 'Player2'
              this.checkWin()
            } else if (this.turn === 'Player2') {
              slot.lastChild.lastChild.style.backgroundColor = "yellow";
              this.turn = 'Player1'
              this.checkWin()
            }
          }
        });
      } else {
        console.log("Column is already full!");
      }
    },
    checkWin() {
      // Horizontal
      for (let i = 0; i < this.map.length; i++) {
        const row = this.map[i];
        for (let j = 0; j < row.length - 3; j++) {
          if (
            row[j] !== 'x' &&
            row[j] === row[j + 1] &&
            row[j] === row[j + 2] &&
            row[j] === row[j + 3]
          ) {
            console.log('Horizontal win for player ' + row[j]);
            this.winner = row[j]
            return;
          }
        }
      }

      // Vertical
      for (let i = 0; i < this.map[0].length; i++) {
        for (let j = 0; j < this.map.length - 3; j++) {
          if (
            this.map[j][i] !== 'x' &&
            this.map[j][i] === this.map[j + 1][i] &&
            this.map[j][i] === this.map[j + 2][i] &&
            this.map[j][i] === this.map[j + 3][i]
          ) {
            console.log('Vertical win for player ' + this.map[j][i]);
            this.winner = this.map[j][i]
            return;
          }
        }
      }

      // Diagonal
      for (let i = 0; i < this.map.length - 3; i++) {
        for (let j = 0; j < this.map[0].length - 3; j++) {
          if (
            this.map[i][j] !== 'x' &&
            this.map[i][j] === this.map[i + 1][j + 1] &&
            this.map[i][j] === this.map[i + 2][j + 2] &&
            this.map[i][j] === this.map[i + 3][j + 3]
          ) {
            console.log('Diagonal win for player ' + this.map[i][j]);
            this.winner = this.map[i][j];
            return;
          }
        }
      }

      // Reverse-Diagonal
      for (let i = 0; i < this.map.length - 3; i++) {
        for (let j = this.map[0].length - 1; j >= 3; j--) {
          if (
            this.map[i][j] !== 'x' &&
            this.map[i][j] === this.map[i + 1][j - 1] &&
            this.map[i][j] === this.map[i + 2][j - 2] &&
            this.map[i][j] === this.map[i + 3][j - 3]
          ) {
            console.log('Reverse-Diagonal win for player ' + this.map[i][j]);
            this.winner = this.map[i][j];
            return;
          }
        }
      }
    }
  }
}

</script>


<style scoped>

.main {
  background-color: #1A1A1A;
  color: #d9d9d9;
}

.red {
  background-color: red;
  box-shadow: unset;
}

.yellow {
  background-color: yellow;
  box-shadow: unset;
}

.round-slot {
  width: 40px;
  height: 40px;
  border-radius: 20px;
  border:black 1px solid;
  background-color: #d9d9d9;
  box-shadow: inset -2px -2px 3px black;
}

.square {
  background-color: rgb(0, 149, 255);
  padding: 10px;
}

.row {
  display:flex;
  margin: auto;
}

.col {
  display:flex;
  margin: auto;

}

</style>
