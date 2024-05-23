<template>
  <h1 data-class="minesweeper-title">
    マインスイーパ
  </h1>
  <div data-class="minesweeper" style="text-align: center;">
    <h2 v-if="this.endText != ''">{{ this.endText }}</h2>
    <div data-class="minesweeper-start-buttons">
      <button class="start-button" @click="start(9, 9, 10)">
        初級
      </button>
      <button class="start-button" @click="start(16, 16, 40)">
        中級
      </button>
      <button class="start-button" @click="start(16, 30, 99)">
        上級
      </button>
    </div>
    <div>
      盤面：{{ this.maxX }} × {{ this.maxY}}　ボムの数：{{ this.numOfBomb }}
    </div>
    
    <div style="margin: 1rem 0;"></div>

    <table data-class="minesweeper-board" class="minesweeper-board">
      <template v-for="(row, xi) in area" :key="xi">
        <tr>
          <template v-for="(cell, yi) in row">
            <td
              v-if="started"
              :key="yi"
              class="cell cell--started"
              :class="{ 'cell--opened': cell.isOpen }"
              @click="open(xi, yi)"
              @click.right.prevent="mark(xi, yi)"
            >
              <span v-if="cell.isOpen">
                {{ cell.isBomb ? "💣" : cell.bombCount === 0 ? "" : cell.bombCount }}
              </span>
              <span v-if="cell.isMark">
                🚩
              </span>
            </td>
            <td
              v-if="!started"
              class="cell cell--unstarted"
              :key="yi"
            >
              &nbsp;
            </td>
          </template>
        </tr>
      </template>    
    </table>
  </div>
  <MinesweeperFooter />
</template>
<script>
import MinesweeperFooter from '@/components/MinesweeperFooter';
/**
 * 盤面上のセル.
 */
class Cell {
  /**
   * 開いているか.
   * @type {Boolean}
   */
  isOpen = false;

  /**
   * 地雷が置かれているか.
   * @type {Boolean}
   */
  isBomb = false;

  /**
   * 旗が立てられているか.
   * @type {Boolean}
   */
  isMark = false;

  /**
   * 周囲の地雷の数.
   * @type {Number}
   */
  bombCount = 0;

  /**
   * 自動で開くことができるか判定して返します.
   * @return {Boolean}
   */
  autoOpenable() {
    return !this.isOpen && !this.isBomb;
  }
}

export default {
  name: "Minesweeper",

  data() {
    return {
      /**
       * ゲームが開始されたか.
       * @type {Boolean}
       */
      started: false,

      /**
       * 盤面.
       * @type {Array}
       */
      area: [],
      
      /**
       * 盤面の行数.
       * @type {Number}
       */
      maxX: 9,
      
      /**
       * 盤面の列数.
       * @type {Number}
       */
      maxY: 9,
      
      /**
       * 地雷の数.
       * @type {Number}
       */
      numOfBomb: 0,

      /**
       * 地雷がない箇所の数.
       * @type {Number}
       */
      numOfNoBomb: 0,

      /**
       * 終了コメント.
       * @type {String}
       */
       endText: '',
    }
  },

  created() {
    this._clearBoard();
  },

  methods: {
    /**
     * マインスイーパを開始します.
     * @param {Number} maxX 行数.
     * @param {Number} maxY 列数.
     * @param {Number} numOfBomb 地雷の数.
     */
    start(maxX, maxY, numOfBomb) {
      this.started = false;

      this.maxX = maxX;
      this.maxY = maxY;
      this.numOfBomb = numOfBomb;
      this.numOfNoBomb = maxX*maxY-numOfBomb;
      this.endText = '';
      
      this._clearBoard();
      this._layMines();

      this.started = true;
    },

    /**
     * セルを開きます.
     * @param {Number} x 何行目.
     * @param {Number} y 何列目.
     */
    open(x, y) {
      // 💣だったら終了
      if (this.area[x][y].isBomb) {
        for (let i = 0; i < this.maxX; i++) {
          for (let j = 0; j < this.maxY; j++) {
            this.area[i][j].isOpen = true;
          }
        }
        this.endText = '負け！また挑戦してね！';
      }

      this.area[x][y].isOpen = true;
      // ボムがないところの数カウントダウン
      if (!this.area[x][y].isBomb) {
        this.numOfNoBomb = this.numOfNoBomb-1;
      }
      // ボムがないところを全部開けた時
      if (this.numOfNoBomb == 0) {
        this.endText = '勝ち！Congratulations!!!';
      }
      if (this.area[x][y].bombCount !== 0) return;

      // 周囲に地雷がない空きセルを開く
      // 左のセル
      if (x-1 >= 0 && this.area[x-1][y].autoOpenable()) {
        this.open(x-1, y);
      }
      // 右のセル
      if (x+1 <= this.maxX-1 && this.area[x+1][y].autoOpenable()) {
        this.open(x+1, y);
      }
      // 上のセル
      if (y-1 >= 0 && this.area[x][y-1].autoOpenable()) {
        this.open(x, y-1);
      }
      // 下のセル
      if (y+1 <= this.maxY-1 && this.area[x][y+1].autoOpenable()) {
        this.open(x, y+1);
      }

      
    },

    /**
     * セルに旗を立てたり、立てなかったりします.
     * @param {Number} x 何行目.
     * @param {Number} y 何列目.
     */
    mark(x, y) {
      if (!this.area[x][y].isOpen) {
        this.area[x][y].isMark = !this.area[x][y].isMark;
      }
    },

    /**
     * 盤面をクリアします.
     */
    _clearBoard() {
      this.area = [];
      for (let i = 0; i < this.maxX; i++) {
        const row = [];
        for (let j = 0; j < this.maxY; j++) {
          row.push(new Cell({isOpen: true}));
        }
        this.area.push(row);
      }      
    },

    /**
     * 地雷を配置します.
     */
    _layMines() {
      // ランダムな整数値を返す
      const getRandomInt = max => {
        return Math.floor(Math.random() * Math.floor(max));
      }

      for (let i = 0; i < this.numOfBomb; i++) {
        const x = getRandomInt(this.maxX);
        const y = getRandomInt(this.maxY);

        // 既に地雷が置かれている場合、別のセルに置くようにする
        if (this.area[x][y].isBomb) {
          i--;
          continue;
        }

        this.area[x][y].isBomb = true;

        // 地雷に隣接するセルの周囲の地雷数を増やす
        // 上のセル
        if (x-1 >= 0) {
          this.area[x-1][y].bombCount++;
          if (y-1 >= 0) this.area[x-1][y-1].bombCount++;
          if (y+1 < this.maxY) this.area[x-1][y+1].bombCount++;
        }

        // 横のセル
        if (y-1 >= 0) this.area[x][y-1].bombCount++;
        if (y+1 < this.maxY) this.area[x][y+1].bombCount++;

        // 下のセル
        if (x+1 < this.maxX) {
          this.area[x+1][y].bombCount++;
          if (y-1 >= 0) this.area[x+1][y-1].bombCount++;
          if (y+1 < this.maxY) this.area[x+1][y+1].bombCount++;
        }
      }      
    },
  }
}
</script>
