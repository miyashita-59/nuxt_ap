<template>
  <div class="board" style="gap: 10px; display: flex;">
    <div v-for="i in 23">
      <p v-if="i%2 == 0 " class="bomb">💣</p>
      <p v-else class="bomb">🚩</p>
    </div>
  </div>
</template>
