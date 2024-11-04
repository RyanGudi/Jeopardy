<template>
    <div class="modal" v-if="isVisible">
        <div class="modal-content">
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
  position: fixed;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
}

.modal-content {
  background-color: white;
  padding: 2rem;
  border-radius: 5px;
  width: 25rem;
  border: 1px solid black;
}
</style>