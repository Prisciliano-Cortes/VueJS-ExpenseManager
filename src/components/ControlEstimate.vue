<script setup>
    import { computed } from 'vue';
    import CircleProgress from 'vue3-circle-progress'
    import "vue3-circle-progress/dist/circle-progress.css"
    import { formatQuantity } from '../helpers'

    const props = defineProps({
        estimate: {
            type: Number,
            required: true
        },
        available: {
            type: Number,
            required: true
        },
        spent: {
            type: Number,
            required: true
        }
    })

    defineEmits([
        "reset-app"
    ])

    const percentage = computed( () => {
        return parseInt((( props.estimate - props.available ) / props.estimate) * 100)
    })
</script>

<template>
    <div class="two-columns">
        <div class="graphic-container">
            <p class="percent">{{ percentage }}%</p>
            <CircleProgress 
                :percent="percentage"
                :size="250"
                :border-width="30"
                :border-bg-width="30"
                fill-color="#3b82f6"
                empty-color="#e1e1e1"

            />
        </div>

        <div class="estimate-container">
            <button 
                class="reset-app"
                @click="$emit('reset-app')"
            >
                Reset app
            </button>

            <p>
                <span>Estimate:</span>
                {{  formatQuantity(estimate) }}
            </p>

            <p>
                <span>Available:</span>
                {{ formatQuantity(available) }}
            </p>

            <p>
                <span>Used:</span>
                {{ formatQuantity(spent) }}
            </p>
        </div>
    </div>
</template>

<style scoped>
    .graphic-container {
        position: relative;

    }

    .percent {
        position: absolute;
        margin: auto;
        top: calc(50% - 1.5rem);
        left: 0;
        right: 0;
        text-align: center;
        z-index: 100%;
        font-size: 3rem;
        font-weight: 900;
        color: var(--gray-black);
    }

    .two-columns {
        display: flex;
        flex-direction: column;
    }

    .two-columns >:first-child {
        margin-bottom: 4rem;
    }

    .estimate-container {
        width: 100%;
    }

    .estimate-container p {
        font-size: 2.4rem;
        text-align: center;
        color: var(--gray-black);
    }

    .estimate-container span {
        font-weight: 900;
        color: var(--blue);
    }

    .reset-app {
        background-color: #DB2777;
        border: none;
        padding: 1rem;
        width: 100%;
        color: var(--white);
        font-weight: 900;
        text-transform: uppercase;
        border-radius: 1rem;
        transition-property: background-color;
        transition-duration: 300ms;
    }

    .reset-app:hover {
        cursor: pointer;
        background-color: #c11d67;

    }

    @media (min-width: 768px) {
        .two-columns {
            flex-direction: row;
            gap: 4rem;
            align-items: center;
        }

        .two-columns >:first-child {
            margin-bottom: 0;
        }

        .estimate-container p {
            font-size: 2.4rem;
            text-align: left;
        }
    }
</style>