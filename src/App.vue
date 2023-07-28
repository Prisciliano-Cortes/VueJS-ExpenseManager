<script setup>
    import { ref, reactive, watch, computed, onMounted } from 'vue';
    import { generateId } from './helpers/index'
    import Used from './components/Used.vue'
    import Modal from './components/Modal.vue'
    import Filter from './components/Filters.vue'
    import Estimate from './components/Estimate.vue'
    import ControlEstimate from './components/ControlEstimate.vue'
    import IconNewUsed from './assets/img/nuevo-gasto.svg'

    const modal = reactive({
        view: false,
        animate: false
    })

    const estimate = ref(0)
    const available = ref(0)
    const spent = ref(0)
    const filter = ref('')

    const used = reactive({
        name: '',
        quantity: '',
        category: '',
        id: null,
        date: Date.now()
    })
    const uses = ref([]) 

    watch(uses, () => {
        const totalUsed = uses.value.reduce((total, use) => use.quantity + total, 0)
        spent.value = totalUsed

        available.value = estimate.value - totalUsed

        localStorage.setItem('uses', JSON.stringify(uses.value))

    }, {deep: true })

    watch(estimate, () => {
        localStorage.setItem('estimate', estimate.value)
    })

    onMounted(() => {
        const estimateStorage = localStorage.getItem('estimate')
        if (estimateStorage ) {
            estimate.value = Number(estimateStorage)
            available.value = Number(estimateStorage)
        }

        const usesStorage = localStorage.getItem('uses')

        if ( usesStorage ) {
            uses.value = JSON.parse(usesStorage)
        }
    })

    const defineEstimate = ( quantity ) => {
        estimate.value = quantity
        available.value = quantity
    }

    const viewModal = () => {
        modal.animate = true,
        setTimeout(() => {
            modal.view = true
        }, 300);
    }

    const closeModal = () => {
        modal.animate = false,
        setTimeout(() => {
            modal.view = false
            restartUsed()
        }, 300);
    }

    const saveUsed = () => {
        if ( used.id ) {
            const { id } = used
            const i = uses.value.findIndex((use) => use.id === id)
            uses.value[i] = { ...used }
        } else {
            uses.value.push({
                ...used,
                id: generateId(),
            })
        }   

        closeModal()

        restartUsed()
    }

    const restartUsed = () => {
        Object.assign(used, {
            name: '',
            quantity: '',
            category: '',
            id: null,
            date: Date.now()
        })
    }

    const selectUsed = (id) => {
        const useEdit = uses.value.find( use => use.id === id )

        Object.assign(used, useEdit)

        viewModal()
    }

    const deleteUsed = (id) => {
        if ( confirm('Delete?') ) {
            uses.value = uses.value.filter( use => use.id !== id)
            closeModal()
        }
    }

    const usedFilter = computed( () => {
        if ( filter.value ) {
            return uses.value.filter( use => use.category === filter.value )
        }

        return uses.value
    })

    const resetApp = () => {
        if (confirm('¿Do you want reset app?')) {
            uses.value = []
            estimate.value = 0
        }
    }
</script>

<template>
    <div
        :class="{ set: modal.view }"
    >
        <header>
            <h1>Expense planner</h1>

            <div class="content-header content shade">
                <Estimate 
                    v-if="estimate === 0"
                    @define-estimate="defineEstimate"
                />

                <ControlEstimate 
                    v-else
                    :estimate="estimate"
                    :available="available"
                    :spent="spent"
                    @reset-app="resetApp"
                />
            </div>
        </header>

        <main v-if="estimate > 0">

            <Filter 
                v-model:filter="filter"
            />

            <div class="list-used content">
                <h2> {{ usedFilter.length > 0 ? 'Uses' : 'No uses' }} </h2>

                <Used 
                    v-for="use in usedFilter"
                    :key="use.id"
                    :use="use"
                    @select-used="selectUsed"
                />
            </div>

            <div class="create-used">
                <img 
                    :src="IconNewUsed"
                    alt="New used"
                    @click="viewModal"
                />
            </div>

            <Modal 
                v-if="modal.view"
                :id="used.id"
                :modal="modal"
                :available="available"
                @hidden-modal="closeModal"
                @save-used="saveUsed"
                @delete-used="deleteUsed"
                v-model:name="used.name"
                v-model:quantity="used.quantity"
                v-model:category="used.category"
            />
        </main>
    </div>
</template>

<style>
    :root {
        --blue: #3b82f6;
        --white: #FFF;
        --gray-light: #F5F5F5;
        --gray: #94a3b8;
        --gray-dark: #64748b;
        --black: #000;
    }

    html {
        font-size: 62.5%;
        box-sizing: border-box;
    }
    *,
    *:before,
    *:after {
        box-sizing: inherit;
    }

    body {
        font-size: 1.6rem;
        font-family: 'Lato', sans-serif;
        background-color: var(--gray-light);
    }

    h1 {
        font-size: 4rem;
    }

    h2 {
        font-size: 3rem;
    }

    .set {
        overflow: hidden;
        height: 100vh;
    }

    header {
        background-color: var(--blue);
    }

    header h1 {
        padding: 3rem 0;
        margin: 0;
        color: var(--white);
        text-align: center;
    }
    
    .content {
        width: 90%;
        max-width: 80rem;
        margin: 0 auto;
    } 

    .content-header {
        margin-top: -5rem;
        transform: translateY(5rem);
        padding: 5rem;
    } 

    .shade {
        box-shadow: 0px 10px 15px -3px rgba(0, 0, 0, 0.1);
        background-color: var(--white);
        border-radius: 1.2rem;
        padding: 5rem;
    }

    .create-used {
        position: fixed;
        bottom: 5rem;
        right: 5rem;
    }

    .create-used img {
        width: 5rem;
        cursor: pointer;
    }

    .list-used {
        margin-top: 10rem;
    }

    .list-used h2 {
        font-weight: 900;
        color: var(--gray-dark);
    }
</style>
