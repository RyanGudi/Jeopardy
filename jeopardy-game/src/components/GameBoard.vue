<template>
    <PlayerInfo 
    :players="players"
    />

    <div class="jeopardy-table-container">
        <div class="jeopardy-table">
            <thead>
                <tr>
                    <th v-for="(category, index) in categories" :key="index" class="category-header">{{ category }}</th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="rowIndex in 3" :key="rowIndex">
                    <td v-for="(category, index) in categories" :key="index" class="question-cell">
                        <button
                            type="button"
                            @click="handleButtonClick(rowIndex, index, getButtonValue(rowIndex))"
                            class="action-button"
                            :disabled="isAnswered(rowIndex, index)"
                            >
                            <span v-if="!isAnswered(rowIndex, index)">{{ `$${getButtonValue(rowIndex)}` }}</span>
                        </button>
                    </td>
                </tr>
            </tbody>
        </div>
    </div>
    <div v-if="loading">Fetching Questions...</div>
    <QuestionModal
        :isVisible="showModal"
        :question="currentQuestion"
        @close="showModal=false"
        @answer="handleAnswer"
    />
    <DoubleJeopardyModal
        :isVisible="showWagerModal"
        :maxWager="Math.max(questionValue, players[currentPlayer].score)"
        @confirm-wager="confirmWager"
        @close="showWagerModal=false"
    />
</template>

<script>
import DoubleJeopardyModal from './DoubleJeopardyModal.vue';
import PlayerInfo from './PlayerInfo.vue';
import QuestionModal from './QuestionModal.vue';

    export default {
        components: {
            QuestionModal,
            PlayerInfo,
            DoubleJeopardyModal
        },
        data() {
            return {
                categories: [],
                displayQuestions: [],
                loading: false,
                sessionToken: null,
                showModal: false,
                currentQuestion: {},
                answeredQuestions: {},
                players: [
                    {"name": "Player 1", "score": 0},
                    {"name": "Player 2", "score": 0},
                    {"name": "Player 3", "score": 0},
                ],
                currentPlayer: 0,
                gameOver: false,
                questionValue: null,
                showWagerModal:false,
                wagerAmount: 0,
                doubleJeopardyCells: [],
            };
        },
        mounted() {
            this.fetchData();
            this.setDoubleJeopardyCells();
        },
        computed: {
            allQuestionsAnswered() {
                return this.categories.length * 3 === Object.keys(this.answeredQuestions).length;
            }
        },

        methods: {
            setDoubleJeopardyCells() {
                this.doubleJeopardyCells = [];
                while (this.doubleJeopardyCells.length < 2) {
                    const row = Math.floor(Math.random() * 3);
                    const col = Math.floor(Math.random() * this.categories.length);
                    const cellKey = `${row}-${col}`;
                    if (!this.doubleJeopardyCells.includes(cellKey)) {
                    this.doubleJeopardyCells.push(cellKey);
                    }
                }
                //console.log("Double Jeopardy Cells: ", this.doubleJeopardyCells);
            },

            isDoubleJeopardy(rowIndex, categoryIndex) {
                return this.doubleJeopardyCells.includes(`${rowIndex}-${categoryIndex}`);
            },

            async handleButtonClick(rowIndex, categoryIndex, value) {
                rowIndex -= 1;
                this.questionValue = value;
                const selectedQuestionObject = this.displayQuestions[categoryIndex][rowIndex];
                //console.log(selectedQuestionObject.question);
                this.currentQuestion = selectedQuestionObject

                if (this.isDoubleJeopardy(rowIndex, categoryIndex)) {
                    //console.log("Double Jeopardy cell clicked!");
                    this.showWagerModal = true;
                } else {
                    this.wagerAmount = value;
                    this.showModal = true;
                }
            },

            confirmWager(wager) {
                this.wagerAmount = wager;
                this.showWagerModal = false;
                this.showModal = true;
            },
            
            declareWinner() {
                const winner = this.players.reduce((topPlayer, player) => {
                    return player.score > topPlayer.score ? player : topPlayer;
                }, this.players[0]);

                alert(`Game Over! ${winner.name} wins with ${winner.score} dollars!`);
                this.gameOver = true;
            },

            handleAnswer(isTrue) {
                const correctAnswer = this.currentQuestion.correct_answer === 'True';

                if(isTrue === correctAnswer) {
                    alert('Correct');
                    this.players[this.currentPlayer].score += this.wagerAmount;
                    //

                } else {
                    alert('Incorrect');
                    this.players[this.currentPlayer].score -= this.wagerAmount;
                    this.currentPlayer = (this.currentPlayer + 1) % this.players.length;
                    //
                }

                // Mark the question as answered
                const categoryIndex = this.categories.indexOf(this.currentQuestion.category);
                const questionIndex = this.displayQuestions[categoryIndex].indexOf(this.currentQuestion);
                this.answeredQuestions[`${questionIndex}-${categoryIndex}`] = true;

                this.currentQuestion = {};
                this.showModal = false;

                //Check if all questions have been answered
                if (this.allQuestionsAnswered && !this.gameOver) {
                    this.declareWinner();
                }
            },

            isAnswered(rowIndex, categoryIndex) {
                return !!this.answeredQuestions[`${rowIndex - 1}-${categoryIndex}`];
            },

            async initializeSession() {
                const tokenResponse = await fetch("https://opentdb.com/api_token.php?command=request");
                const tokenData = await tokenResponse.json();
                this.sessionToken = tokenData.token;
            },

            async fetchData() {
                try {
                    this.loading = true;
                    await this.initializeSession();
                    let categoryNumbers = [];
                    while(categoryNumbers.length < 2) {
                        const randomNumber = Math.floor(Math.random() * 9) + 20;
                        if(!categoryNumbers.includes(randomNumber) && randomNumber != 21 && randomNumber != 27) {
                            categoryNumbers.push(randomNumber);
                        }
                    }

                    for(const categoryNum of categoryNumbers) {
                        const questions = await this.fetchCategoryQuestions(categoryNum);

                        if(questions.length === 3) {
                            this.categories.push(questions[0].category);
                            this.displayQuestions.push(questions);
                        }
                    }
                    this.loading = false;
                    //console.log(`Categories: ${this.categories}`);
                    //console.log('Display Questions:', JSON.stringify(this.displayQuestions, null, 2));
                } catch(error) {
                    console.error('Error fetching data', error);
                }
            },
           
            async fetchCategoryQuestions(categoryNum) {
                const difficulties = ['easy', 'medium', 'hard'];
                // const difficulties = ['easy', 'medium'];
                const questions = [];

                for(const difficulty of difficulties) {
                    const apiUrl = `https://opentdb.com/api.php?amount=1&category=${categoryNum}&difficulty=${difficulty}&type=boolean&token=${this.sessionToken}`;
                    const response = await fetch(apiUrl);
                    const data = await response.json();
                    if(data.results.length) {
                        questions.push(data.results[0]);
                        //console.log(`Category: ${data.results[0].category} Question difficulty: ${difficulty}, Question is: ${data.results[0].question}`);
                    }
                    //API allows a request every 5 seconds
                    await this.delay(5000);
                }
                return questions;
            },
            delay(ms) {
                return new Promise(resolve => setTimeout(resolve, ms));
            },
            getButtonValue(rowIndex) {
                return (rowIndex) * 100;
            }
        }
    }
</script>

<style>
    .jeopardy-table-container {
        padding-top: 1rem;
        background-color: white;
        display: flex;
        justify-content: center;
        align-items: center;
        margin: 0;
    }

    .jeopardy-table {
        border-collapse: collapse;
        font-family: Arial, sans-serif;
        table-layout: fixed;
        width: 60vh;
    }

    .category-header {
        background-color: #0f197b;
        color: white;
        padding: 20px;
        font-size: 1.5rem;
        text-align: center;
        border: 4px solid black;
        width: 25%;
    }

    .question-cell {
        background-color: #0f197b;
        border: 4px solid black;
        height: 100px;
        text-align: center;
        position: relative;
        width: 25%;
        
    }

    .action-button {
        background-color: #0f197b;
        color: #daa453;
        border: none;
        height: 100%;
        width: 100%;
        cursor: pointer;
        font-size: 1.5rem;
        transition: background-color 0.3s, transform 0.3s;
    }

    .action-button:hover {
        background-color: #2a3d8f;
    }



</style>