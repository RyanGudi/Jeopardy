<template>
  <PlayerInfo></PlayerInfo>
  <GameBoard></GameBoard>
</template>

<script>
import PlayerInfo from './components/PlayerInfo.vue';
import GameBoard from './components/GameBoard.vue';


export default {
  name: 'App',
  components: {
    PlayerInfo,
    GameBoard
  }
}
</script>

<style>
/* #app {
} */
</style>
