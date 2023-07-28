<script setup>
    import { ref } from 'vue';
    import Alert from './Alert.vue'

    const estimate = ref(0)
    const error = ref('')

    const emit = defineEmits([
        "define-estimate"
    ])

    const defineEstimate = () => {
        if ( estimate.value <= 0 || estimate.value === '' ) {
            error.value = 'estimate is not valid'

            setTimeout(() => {
                error.value = ''
            }, 2000);

            return
        }

        emit('define-estimate', estimate.value)
    }
</script>

<template>
    <form 
        class="estimate"
        @submit.prevent="defineEstimate"
    >
        <Alert
            v-if="error"
        >
            {{ error }}
        </Alert>

        <div class="field">
            <label for="new-estimate">Define estimate</label>

            <input 
                id="new-estimate"
                class="new-estimate"
                placeholder="Add your estimate"
                type="number"
                min="0"
                v-model.number="estimate"
            />
        </div>
        
        <input 
            type="submit"
            value="Define estimate"
        />
    </form>
</template>

<style scoped>
    .estimate {
        width: 100%;
    }

    .field {
        display: grid;
        gap: 2rem;
    }

    .estimate label {
        font-size: 2.8rem;
        text-align: center;
        color: var(--blue);
    }

    .estimate input[type="number"] {
        background-color: var(--gray-light);
        border-radius: 1rem;
        border: none;
        padding: 1rem;
        font-size: 2.2rem;
        text-align: center;
    }

    .estimate input[type="submit"] {
        background-color: var(--blue);
        border: none;
        padding: 1rem;
        text-align: center;
        font-size: 2rem;
        margin-top: 2rem;
        color: var(--white);
        font-weight: 900;
        width: 100%;
        transition: background-color 300ms ease;
    }

    .estimate input[type="submit"]:hover {
        background-color: #1048A4;
        cursor: pointer;
    }
</style>