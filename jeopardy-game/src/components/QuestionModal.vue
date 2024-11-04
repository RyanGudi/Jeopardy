<template>
    <div class="modal" v-if="isVisible">
        <div class="modal-content">
            <span class="close" @click="closeModal">&times</span>
            <h2>{{ question.category }}</h2>
            <p>{{ question.question }}</p>
            <div>
                <button @click="answer(true)">True</button>
                <button @click="answer(false)">False</button>
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
  display: flex;
  justify-content: center;
  align-items: center;
  position: fixed;
  background-color: rgba(0, 0, 0, 0.5);
}

.modal-content {
  background-color: white;
  padding: 20px;
  border-radius: 5px;
  width: 400px;
}

.close {
  cursor: pointer;
  float: right;
  font-size: 1.5rem;
}
</style>