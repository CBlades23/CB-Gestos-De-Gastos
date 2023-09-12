<script setup>

    import { computed } from 'vue'

    import Swal from 'sweetalert2'

    import cerrarModal from '../assets/img/cerrar.svg'

    const emit = defineEmits(['ocultar-modal', 'guardar-gasto', 'eliminar-gasto' , 'update:nombre', 'update:cantidad', 'update:categoria']);

    const props = defineProps({
        modal: {
            type: Object,
            required: true
        },
        nombre: {
            type: String,
            required: true
        },
        cantidad: {
            type: [String, Number],
            required: true
        },
        categoria: {
            type: String,
            required: true
        },
        disponible: {
            type: Number,
            required: true
        },
        id: {
            type: [String, null],
            required: true
        }

    })

    const old = props.cantidad

    const agregarGasto = () => {
            //Validad que no hayan campos vacíos
            const { nombre, cantidad, categoria, disponible, id } = props 

            if ([nombre, cantidad, categoria].includes('')) {
                
                Swal.fire({
                    position: 'bottom-start',
                    title: 'Debes llenar todos los campos',
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

            //Validar que la cantidad sea mayor a 0
            if (cantidad <= 0) {

                Swal.fire({
                    position: 'bottom-start',
                    title: 'Cantidad no válida',
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

            //Validar que el usuario no gaste más de lo disponible
            if (id) {
                //Tomar en cuenta el gasto que ya se realizó
                if (cantidad > old + disponible) {

                Swal.fire({
                    position: 'bottom-start',
                    title: 'Haz sobrepasado el presupuesto que tienes',
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
            } else {

                if (cantidad > disponible) {

                    Swal.fire({
                        position: 'bottom-start',
                        title: 'Haz sobrepasado el presupuesto que tienes',
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
            }
            
            emit('guardar-gasto')
        }

        const istEditing = computed(() => {
            return props.id
        })

</script>

<template>
    <div class="modal">
        <div class="cerrar-modal">
            <img :src="cerrarModal" @click="$emit('ocultar-modal')" />
        </div>

        <div 
            class="contenedor contenedor-formulario" 
            :class="[modal.animar ? 'animar' : 'cerrar']"
        >
            <form 
                class="nuevo-gasto"
                @submit.prevent="agregarGasto"
            >
                <legend>{{ istEditing ? 'Editar Gasto' : 'Añadir Gasto' }}</legend>

                <div class="campo">
                    <label for="nombre">Nombre</label>
                    <input 
                        type="text" 
                        id="nombre" 
                        placeholder="Añade el nombre del gasto" 
                        :value="nombre"
                        autocomplete="off"
                        @input="$emit('update:nombre', $event.target.value)" 
                    />
                </div>

                <div class="campo">
                    <label for="cantidad">Cantidad</label>
                    <input 
                        type="number" 
                        id="cantidad" 
                        placeholder="Añade la cantidad gastada" 
                        :value="cantidad"
                        @input="$emit('update:cantidad', +$event.target.value)"
                        autocomplete="off"
                    >
                </div>

                <div class="campo">
                    <label for="categoria">Categoria</label>
                    <select 
                        name="categoria" 
                        id="categoria" :value="categoria"
                        @input="$emit('update:categoria', $event.target.value)"
                    >
                            <option value="" disabled>-- Seleccione --</option>
                            <option value="ahorro">Ahorro</option>
                            <option value="comida">Comida</option>
                            <option value="casa">Casa</option>
                            <option value="gastos">Gastos varios</option>
                            <option value="ocio">Ocio</option>
                            <option value="salud">Salud</option>
                            <option value="suscripciones">Suscripciones</option>
                    </select>
                </div>

                <div class="botones">
                    <input 
                    type="submit" 
                    :value="[ istEditing ? 'Guardar Cambios' : 'Añadir Gasto' ]"
                >

                <button
                type="button"
                class="btn-eliminar"
                v-if="istEditing"
                @click="$emit('eliminar-gasto')"
                >
                    Eliminar Gasto
                </button>
                </div>

            </form>


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

.cerrar-modal {
    position: absolute;
    right: 3rem;
    top: 3rem;
}

.cerrar-modal img {
    cursor: pointer;
    width: 3rem;
}

.contenedor-formulario {
    transition-property: all;
    transition-duration: 300ms;
    transition-timing-function: ease-in;
    opacity: 0;
}

.contenedor-formulario.animar {
    opacity: 1;
}

.contenedor-formulario.cerrar {
    opacity: 0;
}

.nuevo-gasto {
    margin: 10rem auto 0 auto;
    display: grid;
    gap: 2.5rem;
}

.nuevo-gasto legend {
    text-align: center;
    color: var(--blanco);
    font-size: 3rem;
    font-weight: 700;
}

.campo {
    display: grid;
    gap: 1.5rem;
}

.nuevo-gasto input,
.nuevo-gasto select {
    background-color: var(--gris-claro);
    padding: 1rem;
    border-radius: 1rem;
    border: none;
    font-size: 2rem;
}

.nuevo-gasto label {
    color: var(--blanco);
    font-size: 2.5rem;
}

.botones {
    display: flex;
    justify-content: space-between;
    gap: 2rem;
}

.nuevo-gasto input[type="submit"] {
    background-color: var(--azul2);
    color: var(--blanco);
    font-weight: 700;
    cursor: pointer;
    width: 100%;
    transition-property: background-color;
    transition-duration: 300ms;
    transition-timing-function: ease-in;
}

.nuevo-gasto input[type="submit"]:hover {
    background-color: var(--azul);
}

.btn-eliminar {
    padding: 1rem;
    width: 100%;
    background-color: var(--rojo2);
    font-weight: 700;
    font-size: 2rem;
    color: var(--blanco);
    border: none;
    cursor: pointer;
    border-radius: 1rem;
    transition-property: background-color;
    transition-duration: 300ms;
    transition-timing-function: ease-in;
}

.btn-eliminar:hover {
    background-color: var(--rojo);
}

</style>
