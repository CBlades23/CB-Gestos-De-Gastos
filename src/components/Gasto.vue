<script setup>

    import { formatearCantidad } from '../helpers'
    import { formatearFecha } from '../helpers'

    import IconoAhorro from '../assets/img/icono_ahorro.svg'
    import IconoCasa from '../assets/img/icono_casa.svg'
    import IconoComida from '../assets/img/icono_comida.svg'
    import IconoGastos from '../assets/img/icono_gastos.svg'
    import IconoOcio from '../assets/img/icono_ocio.svg'
    import IconoSalud from '../assets/img/icono_salud.svg'
    import IconoSuscripciones from '../assets/img/icono_suscripciones.svg'

    const diccionarioIconos = {
        ahorro : IconoAhorro,
        comida : IconoComida,
        casa : IconoCasa,
        gastos : IconoGastos,
        ocio : IconoOcio,
        salud : IconoSalud,
        suscripciones : IconoSuscripciones
    }

    const props = defineProps({
        gasto: {
            type: Object,
            required: true
        }
    })

    defineEmits(['seleccionar-gasto'])

</script>

<template>

    <div class="gasto sombraGasto" @click="$emit('seleccionar-gasto', gasto.id)">

        <div class="contenido">

            <img 
                :src="diccionarioIconos[gasto.categoria]" 
                alt="Icono gasto"
                class="icono"
            >

            <div class="detalles">
                <p class="categoria">{{ gasto.categoria }}</p>
                <p class="nombre">{{ gasto.nombre }}</p>

                <p class="fecha">
                    Fecha:
                    <span>{{ formatearFecha(gasto.fecha) }}</span>
                </p>
            </div>

        </div>

        <p class="cantidad">{{ formatearCantidad(gasto.cantidad) }}</p>

    </div>

</template>

<style scoped>

    .gasto {
        cursor: pointer;
        margin-bottom: 2rem;
        display: flex;
        justify-content: space-between;
        align-items: center;
    } 

    .sombraGasto {
        box-shadow: 0px 10px 15px -3px rgba(0,0,0,0.1);
        background-color: var(--blanco);
        border-radius: 1.2rem;
        padding: 2.5rem 4rem;
    }

    .contenido {
        display: flex;
        gap: 2rem;
        align-items: center;
    }

    .icono {
        width: 7rem;
    }

    .detalle p {
        margin: 0 0 1rem 0;
    }

    .categoria {
        text-transform: capitalize;
        color: var(--gris);
        font-size: 1.4rem;
        font-weight: 900;
        text-transform: uppercase;
    }

    .nombre {
        color: var(--gris-oscuro);
        font-size: 2.4rem;
        font-weight: 700;
    }

    .fecha {
        color: var(--gris-oscuro);
        font-size: 1.6rem;
        font-weight: 900;
    }

    .fecha span {
        font-weight: 400;
    }

    .cantidad {
        font-size: 3rem;
        font-weight: 900;
        margin: 0;
    }

</style>
