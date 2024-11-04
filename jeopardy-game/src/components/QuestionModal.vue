<template>
    <div class="modal" v-if="isVisible">
        <div class="modal-content">
            <h2>{{ question.category }}</h2>
            <p>{{ question.question }}</p>
            <div>
                <button @click="answer(true)" class="btn tbtn">True</button>
                <button @click="answer(false)" class="btn fbtn">False</button>
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        props: {
            isVisible: {
                type: Boolean,
                requires: true,
            },
            question: {
                type: Object,
                required: true,
            }
        },
        methods: {
            closeModal() {
                this.$emit('close');
            },
            answer(isTrue) {
                this.$emit('answer', isTrue);
                this.closeModal();
            }
        }
    }
</script>

<style scoped>
.modal {
    padding-top: .5rem;
    display: flex;
    align-items: center;
    justify-content: center;
}

.modal-content {
    font-family: Arial, sans-serif;
    background-color: white;
    padding: 2rem;
    border-radius: 5px;
    width: 60vh;
    background-color: #0f197b;
    color: #daa453;
    box-shadow: 0px 5px 10px rgba(0, 0, 0, 0.5);
    border-radius: 15px;
}

.btn {
    border: none;
    color:white;
    padding: 0.5rem 1rem;
    border-radius: 10px;
    cursor: pointer;
}

.tbtn {
    background-color: green;
}

.fbtn {
    background-color: red;
    margin-left: 0.1rem;
}
</style>