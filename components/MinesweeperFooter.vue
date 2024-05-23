<template>
  <div class="board">
    <div v-for="i in 23">
      <p v-if="i%2 == 0 " class="bomb">💣</p>
      <p v-else class="bomb">🚩</p>
    </div>
  </div>
</template>
