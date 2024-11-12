<template>
    <div v-if="isVisible" class="modal-overlay">
        <div class="modal-content">
            <h2>Double Jeopardy!</h2>
            <p>Enter your wager (up to {{ maxWager }}):</p>
            <input type="number" v-model="wager" :max="maxWager" :min="0" />
            <button @click="confirmWager">Confirm</button>
            <button @click="$emit('close')">Cancel</button>
        </div>
    </div>
</template>

<script>
export default {
    props: {
        isVisible: Boolean,
        maxWager: Number
    },
    data() {
        return {
            wager: 0
        };
    },
    methods: {
        confirmWager() {
            if (this.wager >= 0 && this.wager <= this.maxWager) {
                this.$emit('confirm-wager', this.wager);
            } else {
                alert(`Please enter a valid wager up to ${this.maxWager}`);
            }
        }
    }
};
</script>

<style>
.modal-overlay {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: rgba(0, 0, 0, 0.5);
    display: flex;
    align-items: center;
    justify-content: center;
}
.modal-content {
    background: white;
    padding: 1rem;
    border-radius: 5px;
    text-align: center;
}
</style>