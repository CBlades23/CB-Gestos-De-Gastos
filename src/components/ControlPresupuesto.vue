<script setup>

    import { computed } from 'vue'

    import imagen from '../assets/img/grafico.jpg'
    import { formatearCantidad } from '../helpers'

    import "vue3-circle-progress/dist/circle-progress.css";
    import CircleProgress from "vue3-circle-progress";

    const props = defineProps({
        presupuesto: {
            type: Number,
            required: true
        },
        disponible: {
            type: Number,
            required: true
        },
        gastado: {
            type: Number,
            required: true
        }
    })

    const porcentaje = computed(() => {
        return parseInt(((props.presupuesto - props.disponible) / props.presupuesto) * 100)
    })

    defineEmits(['reset-app'])

</script>

<template>

    <div class="dos-columnas">
        <div class="contenedor-grafico">

            <p class="porcentaje">{{  porcentaje }}%</p>

            <CircleProgress
                :percent="porcentaje"
                :size="250"
                :border-width="25"
                :border-bg-width="25"
                fill-color='#0065D9'
                empty-color='#F5F5F5'
            />
        </div>

        <div class="contenedor-presupuesto">
            <button 
                class="reset-app"
                type="button"
                @click="$emit('reset-app')"
            > 
                Resetear aplicación
            </button>

            <p>
                <span>Presupuesto:</span>
                {{ formatearCantidad(presupuesto) }}
            </p>

            <p>
                <span>Disponible:</span>
                {{ formatearCantidad(disponible) }}
            </p>

            <p>
                <span>Gastado:</span>
                {{ formatearCantidad(gastado) }}
            </p>
        </div>
    </div>

</template>

<style scoped>

    .contenedor-grafico {
        position: relative;
    }

    .porcentaje {
        position: absolute;
        margin: auto;
        top: calc(50% - 1.5rem);
        left: 0;
        right: 0;
        text-align: center;
        z-index: 100;
        font-size: 3rem;
        font-weight: 900;
        color: var(--gris-oscuro);
    }
    .dos-columnas {
        display: flex;
        flex-direction: column;
    }

    .dos-columnas > :first-child {
        margin-bottom: 3rem;
    }

    @media (width >= 768px) {
        .dos-columnas {
            flex-direction: row;
            gap: 4rem;
            align-items: center;
        }

        .dos-columnas > :first-child {
            margin-bottom: 0;
        }
    }

    .reset-app {
        background-color: #DB2777;
        color: var(--blanco);
        border: none;
        padding: 1rem;
        width: 100%;
        font-weight: 900;
        text-transform: uppercase;
        border-radius: 1rem;
        transition-property: background-color;
        transition-duration: 300ms;
    }

    .reset-app:hover {
        cursor: pointer;
        background-color: #cc2470;
    }

    .contenedor-presupuesto {
        width: 100%;
    }

    .contenedor-presupuesto p {
        font-size: 2.4rem;
        text-align: center;
        color: var(--gris-oscuro);
    }

    @media (width >= 768px) {
        .contenedor-presupuesto p {
            text-align: left;
        }
    }

    .contenedor-presupuesto span {
        font-weight: 900;
        color: var(--azul);
    }



</style>
