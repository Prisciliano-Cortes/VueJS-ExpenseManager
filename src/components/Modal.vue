<script setup>
    import { ref } from 'vue';
    import CloseModal from '../assets/img/cerrar.svg'
    import Alert from './Alert.vue'

    const error = ref('')

    const emit = defineEmits([
        "hidden-modal",
        "save-used",
        "delete-used",
        "update:name",
        "update:quantity",
        "update:category"
    ])

    const props = defineProps({
        modal: {
            type: Object,
            required: true
        },
        id: {
            type: [String, null],
            required: true
        },
        name: {
            type: String,
            required: true
        },
        quantity: {
            type: [String, Number],
            required: true
        },
        category: {
            type: String,
            required: true
        },
        available: {
            type: Number,
            required: true
        }
    })

    const oldQuantity = props.quantity

    const addUsed = () => {
        //*** Validate fields */
        const { name, quantity, category, available, id } = props

        if ( [name, quantity, category].includes('') ) {
            error.value = 'Fields is required'

            setTimeout(() => {
                error.value = ''
            }, 2000)

            return
        }

        if ( quantity <= 0 ) {
            error.value = 'Quantity is not valid'

            setTimeout(() => {
                error.value = ''
            }, 2000)

            return
        }

        //*** Validate that the user does not spend more than what is available */
        if (id) {
            //*** Take into account the expense already made */
            if ( quantity > oldQuantity + available ) {
                error.value = 'You have exceeded the budget'

                setTimeout(() => {
                    error.value = ''
                }, 2000)

                return
            }
        } else {
            if ( quantity > available ) {
                error.value = 'You have exceeded the budget'

                setTimeout(() => {
                    error.value = ''
                }, 2000)

                return
            }
        }

        emit('save-used')
    }
</script>

<template>
    <div class="modal">
        <div class="close-modal">
            <img 
                :src="CloseModal"
                alt="close-modal"
                @click="$emit('hidden-modal')"
            />
        </div>

        <div 
            :class="[modal.animate ? 'animate' : 'close']"
            class="content content-form"
        >
            <form 
                class="new-used"
                @submit.prevent="addUsed"
            >
                <legend>{{ id ? 'Edit used' : 'Add used' }}</legend>

                <Alert v-if="error">
                    {{ error }}
                </Alert>

                <div class="field">
                    <label for="name">Name used:</label>
                    <input 
                        type="text"
                        id="name"
                        placeholder="Add name used"
                        :value="name"
                        @input="$emit('update:name', $event.target.value)"
                    />
                </div>

                <div class="field">
                    <label for="quantity">Quantity used:</label>
                    <input 
                        type="number"
                        id="quantity"
                        placeholder="Add quantity used"
                        :value="quantity"
                        @input="$emit('update:quantity', +$event.target.value)"
                    />
                </div>

                <div class="field">
                    <label for="category">Category used:</label>
                    <select 
                        id="category"
                        :value="category"
                        @input="$emit('update:category', $event.target.value)"
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

                <input 
                    type="submit"
                    :value="[ id ? 'Edit used' : 'Add used']"
                />
            </form>

            <button
                type="button"
                class="btn-delete"
                v-if="id"
                @click="$emit('delete-used', id)"
            >
                Delete used
            </button>
        </div>
    </div>
</template>

<style scoped>
    .modal {
        position: absolute;
        background-color: rgb(0 0 0 / 0.9);
        top: 0;
        left: 0;
        right: 0;
        bottom: 0;
    }

    .close-modal {
        position: absolute;
        right: 3rem;
        top: 3rem;
    }

    .content-form {
        transition-property: all;
        transition-duration: 300ms;
        transition-timing-function: ease-in;
        opacity: 0;
    }

    .content-form.animate {
        opacity: 1;
    }

    .content-form.close {
        opacity: 0;
    }

    .close-modal img {
        width: 3rem;
        cursor: pointer;
    }

    .new-used {
        margin: 10rem auto 0 auto;
        display: grid;
        gap: 2rem;
    }

    .field {
        display: grid;
        gap: 2rem;
    }

    .new-used input, .new-used select {
        background-color: var(--gray-light);
        border-radius: 1rem;
        padding: 1rem;
        border: none;
        font-size: 2.2rem;
    }

    .new-used label {
        color: var(--white);
    }

    .new-used input[type='submit'] {
        background-color: var(--blue);
        color: var(--white);
        font-weight: 700;
        cursor: pointer;
    }

    .new-used legend {
        text-align: center;
        color: var(--white);
        font-size: 3rem;
        font-weight: 700;
    }

    .btn-delete {
        padding: 1rem;
        width: 100%;
        background-color: #ef4444;
        font-weight: 700;
        font-size: 1.2rem;
        color: var(--white);
        margin-top: 10rem;
        cursor: pointer;
        border: none;
    }
</style>