<template>
  <div id="app">
    <input type="file" @change="uploadImage">
    <input type="number" min="3" max="10" v-model="boardSize">
    <div class="board">
      <div v-for="(row,rindex) in board" :key="rindex" class="board-row">
        <div
          v-for="(panel, pindex) in row"
          :key="pindex"
          class="board-panel"
          @click="move(panel)"
          :class="{'correct-panel': isCorrectPanel(panel)}"
        >
          <PanelCanvas
            :key="pindex"
            :baseSrc="baseSrc"
            :x="panel.displayPosition.x"
            :y="panel.displayPosition.y"
            :number="panel.number"
            :size="boardSize"
          ></PanelCanvas>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import PanelCanvas from "@/PanelCanvas.vue";

export default {
  name: "app",
  components: {
    PanelCanvas
  },
  data() {
    return {
      boardSize: 3,
      board: [],
      baseSrc: ""
    };
  },
  created() {
    // this.initialBoard();
  },
  methods: {
    initialBoard() {
      for (let y = 0; y < this.boardSize; y++) {
        let row = [];
        for (let x = 0; x < this.boardSize; x++) {
          const panel = this.createPanel(x, y, y * this.boardSize + x + 1);
          row.push(panel);
        }
        this.board.push(row);
      }

      //Shuffle!
      this.shuffleBoard();
    },
    createPanel(x, y, number) {
      return {
        originalPosition: {
          x: x,
          y: y
        },
        displayPosition: {
          x: x,
          y: y
        },
        number: number
      };
    },
    move(selectedPanel) {
      //空きパネルがあるか上下左右方向を走査する
      const x = selectedPanel.originalPosition.x;
      const y = selectedPanel.originalPosition.y;
      const number = selectedPanel.number;

      //上方向を走査する
      this.swapPanelStatus(selectedPanel, this.board, y - 1, x, () => {
        return y !== 0;
      });
      //下方向を走査する
      this.swapPanelStatus(selectedPanel, this.board, y + 1, x, () => {
        return y !== this.boardSize - 1;
      });
      //左方向を走査する
      this.swapPanelStatus(selectedPanel, this.board, y, x - 1, () => {
        return x !== 0;
      });
      //右方向を走査する
      this.swapPanelStatus(selectedPanel, this.board, y, x + 1, () => {
        return x !== this.boardSize - 1;
      });
    },
    isEmptyPanel(number) {
      return Math.pow(this.boardSize, 2) === number;
    },
    isCorrectPanel(panel) {
      return (
        panel.displayPosition.x === panel.originalPosition.x &&
        panel.displayPosition.y === panel.originalPosition.y
      );
    },
    swapPanelStatus(select, board, y, x, checkFunc) {
      if (!checkFunc()) {
        return;
      }
      let boardPanel = board[y][x];
      if (!this.isEmptyPanel(boardPanel.number)) {
        return;
      }

      if (select && boardPanel) {
        //xy座標numberを交換
        [select.number, boardPanel.number] = [boardPanel.number, select.number];
        [select.displayPosition.x, boardPanel.displayPosition.x] = [
          boardPanel.displayPosition.x,
          select.displayPosition.x
        ];
        [select.displayPosition.y, boardPanel.displayPosition.y] = [
          boardPanel.displayPosition.y,
          select.displayPosition.y
        ];
      }
    },
    shuffleBoard() {
      let from;
      let to;
      let fromY = 0;
      let toY = 0;
      let fromX = 0;
      let toX = 0;
      let count = 0;
      while (count < Math.pow(this.boardSize, 2)) {
        fromX = Math.floor(Math.random() * this.boardSize);
        fromY = Math.floor(Math.random() * this.boardSize);
        toX = Math.floor(Math.random() * this.boardSize);
        toY = Math.floor(Math.random() * this.boardSize);

        from = this.board[fromY][fromX].displayPosition;
        to = this.board[toY][toX].displayPosition;
        [from.x, to.x] = [to.x, from.x];
        [from.y, to.y] = [to.y, from.y];
        count++;
      }
    },
    uploadImage(e) {
      this.initialBoard();
      let vm = this;
      let input = e.target;
      if (input.files && input.files[0]) {
        let reader = new FileReader();

        reader.onload = function(e) {
          vm.baseSrc = e.target.result;
        };

        reader.readAsDataURL(input.files[0]);
        e.disable = true;
      }
    }
  }
};
</script>

<style scoped>
#app {
  width: 1300px;
  background: rgba(0, 0, 0, 0.1);
  margin: 0 auto;
  padding: 10px;
}

.board {
  width: 1200px;
  padding: 10px;
}

.board-row {
  height: 80px;
  margin-bottom: 2px;
}

.board-panel {
  display: inline-block;
  width: 80px;
  height: inherit;
  border: 2px solid transparent;
}

.correct-panel {
  border: 2px solid yellow;
}
</style>
