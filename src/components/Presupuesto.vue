<script setup>

    import { ref } from 'vue'
    import Swal from 'sweetalert2'

    const presupuesto = ref(0)
    const error = ref('')

    const emit = defineEmits(['definir-presupuesto'])

    const definirPresupuesto = () => {
        if (presupuesto.value <= 0 || presupuesto.value === '') {
            Swal.fire({
                position: 'bottom-start',
                title: 'Presupuesto no válido',
                showConfirmButton: false,
                timer: 111500,
                background: '#b91c1c',
                color: '#FFF',
                customClass: 'errorAlert',
                timer: 4000,
                backdrop: false
            })

            return
        }

        emit('definir-presupuesto', presupuesto.value)

    }

</script>

<template>
    <form class="presupuesto" @submit.prevent="definirPresupuesto">

        <div class="campo">
            <label for="nuevo-presupuesto">Presupuesto</label>
            <input 
                type="number" 
                id="nuevo-presupuesto" 
                class="nuevo-presupuesto" 
                placeholder="Añade tu presupuesto" 
                min="0"
                v-model.number="presupuesto"
                autocomplete="off"
            >
        </div>

        <input type="submit" value="Definir Presupuesto">

    </form>
</template>

<style scoped>
    .presupuesto {
        width: 100%;
    }

    .campo {
        display: grid;
        gap: 2rem;
        margin-bottom: 2rem;
    }

    .presupuesto label {
        font-size: 2.3rem;
        text-align: center;
        color: var(--azul);
    }

    .presupuesto input[type="number"] , .presupuesto input[type="text"] {
        background-color: var(--gris-claro);
        border: none;
        padding: 1rem;
        font-size: 2.2rem;
        text-align: center;
    }

    .presupuesto input[type="submit"] {
        background-color: var(--azul2);
        border: none;
        padding: 1rem;
        text-align: center;
        border-radius: .5rem;
        font-size: 2rem;
        margin-top: 2rem;
        color: var(--blanco);
        width: 100%;
        font-weight: 900;
        transition: background-color 300ms ease;
    }

    .presupuesto input[type="submit"]:hover {
        background-color: var(--azul);
        cursor: pointer;
    }



</style>
