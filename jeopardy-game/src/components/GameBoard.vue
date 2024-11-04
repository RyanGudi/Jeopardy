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
                        <button type="button" @click="handleButtonClick(rowIndex, index, getButtonValue(rowIndex))" class="action-button">{{getButtonValue(rowIndex)}}</button>
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
</template>

<script>
import PlayerInfo from './PlayerInfo.vue';
import QuestionModal from './QuestionModal.vue';

    export default {
        components: {
            QuestionModal,
            PlayerInfo
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
            };
        },
        mounted() {
            this.fetchData();
        },
        methods: {
            async handleButtonClick(rowIndex, categoryIndex, value) {
                rowIndex -= 1;
                this.questionValue = value;
                const selectedQuestionObject = this.displayQuestions[categoryIndex][rowIndex];
                console.log(selectedQuestionObject.question);
                this.currentQuestion = selectedQuestionObject
                this.showModal = true;
            },

            handleAnswer(isTrue) {
                const correctAnswer = this.currentQuestion.correct_answer === 'True';

                if(isTrue === correctAnswer) {
                    alert('Correct');
                    this.players[this.currentPlayer].score += this.questionValue;
                    //

                } else {
                    alert('Incorrect');
                    this.players[this.currentPlayer].score -= this.questionValue;
                    this.currentPlayer = (this.currentPlayer + 1) % this.players.length;
                    //
                }
                this.currentQuestion = {};
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
                    console.log(`Categories: ${this.categories}`);
                    console.log('Display Questions:', JSON.stringify(this.displayQuestions, null, 2));
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