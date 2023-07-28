<script setup>
    import { formatQuantity, formatDate } from '../helpers/index'
    import IconoAhorro from '../assets/img/icono_ahorro.svg'
    import IconoCasa from '../assets/img/icono_casa.svg'
    import IconoComida from '../assets/img/icono_comida.svg'
    import IconoGastos from '../assets/img/icono_gastos.svg'
    import IconoOcio from '../assets/img/icono_ocio.svg'
    import IconoSalud from '../assets/img/icono_salud.svg'
    import IconoSuscripciones from '../assets/img/icono_suscripciones.svg'
    
    const dictionaryIcons = {
        savingMoney : IconoAhorro,
        food : IconoComida,
        home : IconoCasa,
        miscellaneousExpenses : IconoGastos,
        leisure : IconoOcio,
        health : IconoSalud,
        subscriptions : IconoSuscripciones
    }

    defineEmits([
        "select-used"
    ])

    const props = defineProps({
        use: {
            type: Object,
            required: true 
        }
    })
</script>

<template>
    <div class="used shade">
        <div class="contents">
            <img 
                :src="dictionaryIcons[use.category]"
                alt="Icon use"
                class="icon"
            />
            <div class="details">
                <p class="category">{{ use.category }}</p>
                <p 
                    class="name"
                    @click="$emit('select-used', use.id)"
                >
                    {{ use.name }}
                </p>

                <p class="date">
                    Date:
                    <span>
                        {{ formatDate(use.date) }}
                    </span>
                </p>
            </div>
        </div>

        <p class="quantity">{{ formatQuantity(use.quantity) }}</p>
    </div>
</template>

<style scoped>
    .used {
        display: flex;
        justify-content: space-between;
        align-items: center;
        margin-bottom: 2rem;
    }

    .contents {
        display: flex;
        gap: 2rem;
        align-items: center;
    }

    .icon {
        width: 5rem;
    }

    .details p {
        margin: 0 0 1rem 0;
    }

    .category {
        color: var(--gray);
        font-size: 1.2rem;
        text-transform: uppercase;
        font-weight: 900;
    }

    .name {
        color: var(--gray-dark);
        font-size: 2.4rem;
        font-weight: 700;
        cursor: pointer;
    }

    .date {
        font-size: 1.6rem;
        font-weight: 900;
        color: var(--gray-dark);
    }

    .date span {
        font-weight: 400;
    }

    .quantity {
        font-size: 3rem;
        font-weight: 900;
        margin: 0;
    }
</style>