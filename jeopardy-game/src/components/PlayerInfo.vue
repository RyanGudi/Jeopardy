<template>
    <h1>Jeopardy</h1>
    <div class="players-container">
        <div class="playerCard">
            <p>Player 1</p>
            <h2>Score</h2>
        </div>
        <div class="playerCard">
            <p>Player 2</p>
            <h2>Score</h2>
        </div>
        <div class="playerCard">
            <p>Player 3</p>
            <h2>Score</h2>
        </div>
    </div>
</template>

<script>
    
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