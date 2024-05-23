<template>
  <div class="board">
    <section v-for="(b, i) in blocks" :key="i">
      <div v-for="(d, j) in b" :key="j">
        <table>
          <tr v-for="(h, hi) in d" :key="hi">
            <td v-for="(v, vi) in h" :key="vi" class="block" :class="blockClass(v)"></td>
          </tr>
        </table>
      </div>
    </section>
  </div>
</template>
<script>
const blocks = {
  1: {
    data: [
      [0,0,1,0],
      [0,0,1,0],
      [0,0,1,0],
      [0,0,1,0],
    ],
  },
  2: {
    data: [
      [0,0,0,0],
      [0,2,2,0],
      [0,2,2,0],
      [0,0,0,0],
    ],
  },
  3: {
    data: [
      [0,0,0,0],
      [0,0,3,0],
      [0,3,3,3],
      [0,0,0,0],
    ],
  },
  4: {
    data: [
      [0,0,0,0],
      [0,0,4,0],
      [0,0,4,0],
      [0,4,4,0],
    ],
  },
  5: {
    data: [
      [0,0,0,0],
      [0,5,0,0],
      [0,5,0,0],
      [0,5,5,0],
    ],
  },
  6: {
    data: [
      [0,0,0,0],
      [0,0,6,6],
      [0,6,6,0],
      [0,0,0,0],
    ],
  },
  7: {
    data: [
      [0,0,0,0],
      [0,7,7,0],
      [0,0,7,7],
      [0,0,0,0],
    ],
  }
}

export default {
  data() {
    return {
      blocks: blocks,
    }
  },
  methods: {
    blockClass(val) {
      switch(val) {
        case 1: return '-i';
        case 2: return '-o';
        case 3: return '-t';
        case 4: return '-j';
        case 5: return '-l';
        case 6: return '-s';
        case 7: return '-z';
        default: return '';
      }
    }
  }
}
</script>