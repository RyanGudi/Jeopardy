<template>
    <div>
        <h1>Jeopardy</h1>
        <div class="players-container">
            <div class="playerCard" v-for="(player, index) in players" :key="index">
                <p>{{ player.name }}</p>
                <h2>{{ `$${player.score}` }}</h2>
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        props: {
            players: {
                type: Array,
                required: true
            }
        }
    }
</script>

<style>
    h1 {
        text-align: center;
    }

    .players-container {
        font-family: Arial, sans-serif;
        display: flex;
        justify-content: center;
        gap: 3rem;
    }

    .players-container h2 {
        padding-right: 3rem;
    }

    .playerCard {
        display: flex;
        justify-content: space-between;
        align-items: center;
        color: #daa453;
        background-color: #0f197b;
        width: 20%;
        box-shadow: 0px 5px 10px rgba(0, 0, 0, 0.5);
        border-radius: 15px;
        padding: 1rem;
    }
</style>