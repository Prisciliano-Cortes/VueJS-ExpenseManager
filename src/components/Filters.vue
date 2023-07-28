<script setup>
    defineProps({
        filter: {
            type: String
        }
    })

    defineEmits([
        'update:filter'
    ])
</script>

<template>
    <div class="filters shade content">
        <form>
            <div class="field">
                <label for="filter">Filter used</label>

                <select 
                    id="filter"
                    :value="filter"
                    @input="$emit('update:filter', $event.target.value)"
                >
                    <option value=""> -- Select category -- </option>
                    <option value="savingMoney"> Saving-money </option>
                    <option value="food"> Food </option>
                    <option value="home"> Home </option>
                    <option value="miscellaneousExpenses"> Miscellaneous expenses </option>
                    <option value="leisure"> Leisure </option>
                    <option value="health"> Health </option>
                    <option value="subscriptions"> Subscriptions </option>
                </select>
            </div>
        </form>
    </div>
</template>

<style scoped>
    .filters {
        margin-top: 10rem;
    }

    .filters .field {
        display: flex;
        align-items: center;
        gap: 2rem;
    }

    .filters label {
        font-size: 3rem;
        font-weight: 900;
        color: var(--gray-dark);
    }

    .filters select {
        flex: 1;
        padding: 1rem;
        border: none;
        border-radius: 1rem;
        background-color: var(--gray-light);
    }
</style>