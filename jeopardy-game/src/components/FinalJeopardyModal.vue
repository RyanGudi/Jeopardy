<template>
    <div class="modal-overlay">
      <div class="modal-content">
        <h2>Final Jeopardy: {{ category }}</h2>
        
        <div v-if="!questionVisible">
          <h3>Place Your Wagers</h3>
          <div v-for="(player, index) in players" :key="player.name" class="player-wager">
            <p>{{ player.name }} - Current Balance: ${{ player.score }}</p>
            <input
              v-model.number="wagers[index]"
              :max="player.score"
              type="number"
              placeholder="Enter wager"
              :disabled="player.score <= 0"
            />
            <button @click="submitWager(index)" :disabled="player.score <= 0 || wagers[index] < 0 || wagers[index] > player.score">
              Confirm Wager
            </button>
          </div>
        </div>
  
        <div v-if="questionVisible">
          <h3>Final Jeopardy Question</h3>
          <p>{{ question.question }}</p>
          <div v-for="(answer, answerIndex) in shuffledAnswers" :key="answerIndex" class="answer-option">
            <input
              type="radio"
              :name="'answer-' + playerIndex"
              :value="answer"
              v-model="answers[playerIndex]"
            />
            <label>{{ answer }}</label>
          </div>
          <button @click="submitAnswer(playerIndex)" :disabled="!answers[playerIndex]">Submit Answer</button>
        </div>
  
        <button v-if="!questionVisible && allWagersSubmitted" @click="showFinalQuestion">Reveal Final Question</button>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    props: {
      category: String,
      players: Array,
      question: Object
    },
    data() {
      return {
        wagers: Array(this.players.length).fill(0),
        answers: Array(this.players.length).fill(''),
        questionVisible: false,
        shuffledAnswers: []
      };
    },
    computed: {
      allWagersSubmitted() {
        return this.wagers.every((wager, index) => wager >= 0 && wager <= this.players[index].score);
      }
    },
    watch: {
        question: {
            immediate: true,
            handler(newQuestion) {
                if (newQuestion && newQuestion.correct_answer && newQuestion.incorrect_answers) {
                this.shuffledAnswers = this.shuffleArray([newQuestion.correct_answer, ...newQuestion.incorrect_answers]);
                this.questionVisible = true;
                }
            },
        },
    },
    methods: {
        showFinalQuestion() {
            this.questionVisible = true;
        },
        submitWager(playerIndex) {
            this.$emit('submit-wager', playerIndex, this.wagers[playerIndex]);
        },
        submitAnswer(playerIndex) {
            this.$emit('submit-answer', playerIndex, this.answers[playerIndex]);
        },
        shuffleArray(array) {
            return array.sort(() => Math.random() - 0.5);
        }
    }
  };
  </script>
  
  <style scoped>
  .modal-overlay {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background: rgba(0, 0, 0, 0.5);
    display: flex;
    justify-content: center;
    align-items: center;
  }
  .modal-content {
    background: white;
    padding: 20px;
    border-radius: 8px;
    width: 500px;
    max-width: 90%;
  }
  .player-wager {
    margin-bottom: 10px;
  }
  .answer-option {
    margin: 8px 0;
  }
  button {
    margin-top: 10px;
  }
  </style>
  