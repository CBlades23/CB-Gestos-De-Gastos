<script setup>

/*IMPORTAR LIBRERÍAS*/
import { ref, reactive, watch, computed, onMounted } from 'vue'
import Swal from 'sweetalert2'

/*IMPORTAR COMPONENTES O ARCHIVOS*/
import Presupuesto from './components/Presupuesto.vue';
import ControlPresupuesto from './components/ControlPresupuesto.vue';
import Modal from './components/Modal.vue';
import Gasto from './components/Gasto.vue'
import Filtros from './components/Filtros.vue'
import { generarId } from './helpers'

import iconoNuevoGasto from './assets/img/nuevo-gasto.svg'

const modal = reactive({
  mostrar: false,
  animar: false
})

const presupuesto = ref(0)
const disponible = ref(0)
const gastado = ref(0)
const filtro = ref('')
const nombre = ref('')

const gasto = reactive({
  nombre: '',
  cantidad: '',
  categoria: '',
  id: null,
  fecha: Date.now()
})

const gastos = ref([])

watch(gastos, () => {
  const totalGastado = gastos.value.reduce((total, gasto) => gasto.cantidad + total, 0)
  gastado.value = totalGastado

  disponible.value = presupuesto.value - totalGastado

  localStorage.setItem('gastos', JSON.stringify(gastos.value))
}, {
  deep: true
})

watch(modal, () => {
  if (!modal.mostrar) {
    //Reiniciar el objeto de gasto
    reiniciarStateGasto()
  }
}, {
  deep: true
})

//LOCAL STORAGE
watch(presupuesto, () => {
  localStorage.setItem('presupuesto', presupuesto.value)
})

watch(disponible, () => {


  if (disponible.value === 0) {

    setTimeout(() => {

      Swal.fire({
        position: 'bottom-start',
        title: 'Ya no tienes presupuesto disponible',
        showConfirmButton: false,
        timer: 111500,
        background: '#b91c1c',
        color: '#FFF',
        customClass: 'errorAlert',
        timer: 4000,
        backdrop: false
      })
    }, 400);

  }

}, {
  deep: true
})

onMounted(() => {
  const presupuestoStorage = localStorage.getItem('presupuesto')
  if (presupuestoStorage) {
    presupuesto.value = Number(presupuestoStorage)
    disponible.value = Number(presupuestoStorage)
  }

  const gastosStorage = localStorage.getItem('gastos')
  if (gastosStorage) {
    gastos.value = JSON.parse(gastosStorage)
  }
})

const definirPresupuesto = (cantidad) => {
  presupuesto.value = cantidad
  disponible.value = cantidad
}

const mostrarModal = () => {
  modal.mostrar = true
  setTimeout(() => {
    modal.animar = true
  }, 300);
}

const ocultarModal = () => {
  modal.animar = false
  setTimeout(() => {
    modal.mostrar = false
  }, 300);
}

const guardarGasto = () => {
  if (gasto.id) {
    // editando
    const { id } = gasto
    const i = gastos.value.findIndex((gasto => gasto.id === id))
    gastos.value[i] = { ...gasto }
  } else {
    //registro nuevo
    gastos.value.push({
      ...gasto,
      id: generarId(),
    })
  }

  //Ocultar el modal luego de dar click en guardar
  ocultarModal();

  if (gasto.id) {
    //Mensaje de que se guardó correctamente
    setTimeout(() => {
      Swal.fire({
        position: 'bottom-start',
        title: 'Gasto editado correctamente',
        showConfirmButton: false,
        timer: 111500,
        background: '#38b000',
        color: '#FFF',
        customClass: 'goodAlert',
        timer: 40000,
        backdrop: false
      })
    }, 400);

    //Reiniciar el objeto de gasto
    reiniciarStateGasto()
  } else {
    //Mensaje de que se editó correctamente
    setTimeout(() => {
      Swal.fire({
        position: 'bottom-start',
        title: 'Gasto guardado correctamente',
        showConfirmButton: false,
        timer: 111500,
        background: '#38b000',
        color: '#FFF',
        customClass: 'goodAlert',
        timer: 4000,
        backdrop: false
      })
    }, 400);

    //Reiniciar el objeto de gasto
    reiniciarStateGasto()
  }
}

const seleccionarGasto = id => {
  const gastoEditar = gastos.value.filter(gasto => gasto.id === id)[0]
  Object.assign(gasto, gastoEditar);
  mostrarModal()
}

const reiniciarStateGasto = () => {
  //Reiniciar el objeto de gasto
  Object.assign(gasto, {
    nombre: '',
    cantidad: '',
    categoria: '',
    id: null,
    fecha: Date.now()
  })
}

const eliminarGasto = () => {

  Swal.fire({
    title: '¿Quieres eliminarlo?',
    text: "Si lo eliminas, no podrás revertirlo",
    icon: 'warning',
    showCancelButton: true,
    confirmButtonColor: '#3085d6',
    cancelButtonColor: '#d33',
    confirmButtonText: 'Eliminar',
    cancelButtonText: 'Cancelar',
    customClass: 'bgAlert'
  }).then((result) => {
    if (result.isConfirmed) {

      gastos.value = gastos.value.filter(gastoState => gastoState.id !== gasto.id);
      ocultarModal();

      setTimeout(() => {

        Swal.fire({
          position: 'bottom-start',
          title: 'Gasto eliminado correctamente',
          showConfirmButton: false,
          timer: 111500,
          background: '#38b000',
          color: '#FFF',
          customClass: 'goodAlert',
          timer: 4000,
          backdrop: false
        })
      }, 450);

    }
  })

}

const gastosFiltrados = computed(() => {
  if (filtro.value) {
    return gastos.value.filter(gasto => gasto.categoria === filtro.value)
  }
  return gastos.value
})

const resetApp = (() => {
  Swal.fire({
    title: '¿Quieres reiniciar tu presupuesto y gastos?',
    text: "Si lo reinicias, no podrás revertirlo",
    icon: 'warning',
    showCancelButton: true,
    confirmButtonColor: '#3085d6',
    cancelButtonColor: '#d33',
    confirmButtonText: 'Reiniciar',
    cancelButtonText: 'Cancelar',
    customClass: 'bgAlert'
  }).then((result) => {
    if (result.isConfirmed) {

      gastos.value = []
      presupuesto.value = 0;  
    }
  })
})

const definirNombre = (nombre) => {
        nombreUsuario.value = nombre;
    }

</script>

<template>
  <div :class="{ fijar: modal.mostrar }">
    <header>
      <h1>Gestor de gastos</h1>

      <div class="contenedor-header contenedor sombra">

        <Presupuesto 
          v-if="presupuesto === 0"
          @definir-presupuesto="definirPresupuesto" 
        />

        <ControlPresupuesto 
          v-else 
          :presupuesto="presupuesto" 
          :disponible="disponible" 
          :gastado="gastado" 
          @reset-app="resetApp"

        />

      </div>

    </header>

    <main v-if="presupuesto > 0">

      <Filtros v-model:filtro="filtro" />

      <div class="listado-gastos contenedor">

        <h2>{{ gastosFiltrados.length > 0 ? 'Gastos' : 'No hay gastos' }}</h2>
        <p class="descripcion">{{ gastosFiltrados.length > 0 ? 'Para eliminar o editar un gasto da click encima del mismo' : '' }}</p>
        

        <Gasto v-for="gasto in gastosFiltrados" :key="gasto.id" :gasto="gasto" @seleccionar-gasto="seleccionarGasto" />

      </div>

      <div class="crear-gasto">

        <img :src="iconoNuevoGasto" alt="Añadir nuevo gasto" @click="mostrarModal" />

      </div>

      <Modal v-if="modal.mostrar" @ocultar-modal="ocultarModal" @guardar-gasto="guardarGasto"
        @eliminar-gasto="eliminarGasto" :modal="modal" :id="gasto.id" :disponible="disponible"
        v-model:nombre="gasto.nombre" v-model:cantidad="gasto.cantidad" v-model:categoria="gasto.categoria" />

    </main>

  </div>

</template>

<style>
:root {
  --azul: #0065D9;
  --azul2: #248aff;
  --rojo: #b91c1c;
  --rojo2: #eb2525;
  --blanco: #FFF;
  --gris-claro: #F5F5F5;
  --gris-claro2: #e1e1e1;
  --gris: #94a3b8;
  --gris-oscuro: #64748b;
  --negro: #000;
}

html {
  font-size: 62.5%;
  box-sizing: border-box;
}

*,
*:before,
*::after {
  box-sizing: inherit;
}

body {
  font-size: 1.6rem;
  font-family: "Lato", sans-serif;
  background-color: var(--gris-claro);
}

h1 {
  font-size: 4rem;
}

h2 {
  font-size: 3rem;
}

.fijar {
  overflow: hidden;
  height: 100vh;
}

header {
  background-color: var(--azul);
}

header h1 {
  padding: 3rem 0;
  margin: 0;
  color: var(--blanco);
  text-align: center;
}

.contenedor {
  width: 90%;
  max-width: 80rem;
  margin: 0 auto;
}

.contenedor-header {
  margin-top: -5rem;
  transform: translateY(5rem);
  padding: 5rem;
}

.sombra {
  box-shadow: 0px 10px 15px -3px rgba(0, 0, 0, 0.1);
  background-color: var(--blanco);
  border-radius: 1.2rem;
  padding: 5rem;
}

.swal2-container > * {
  background: none !important;
}

.swal2-show {
  background-color: var(--blanco);
  font-size: 1.6rem;
  padding: 1.3rem !important;
}

@media (width <=768px) {
  .swal2-show {
    font-size: 1.3rem;
  }
}

.descripcion {
  color: var(--gris);
  font-size: 2rem;
  font-weight: 500;
}

.crear-gasto {
  position: fixed;
  bottom: 5rem;
  right: 5rem;
  z-index: 1;
}

.crear-gasto img {
  width: 5rem;
  cursor: pointer;
}

.listado-gastos {
  margin-top: 10rem;

}

.listado-gastos h2 {
  font-weight: 900;
  font-size: 4rem;
  color: var(--gris-oscuro);
}

.goodAlert {
  width: 40rem;
  background-color: #38b000 !important;
}

.goodAlert h2 {
  font-size: 1.6rem;
  padding: 0;
}

.errorAlert {
  width: 40rem;
  background-color: #b91c1c !important;
}

.errorAlert h2 {
  font-size: 1.6rem;
  padding: 0;
}

.bgAlert {
  background-color: var(--blanco) !important;
}

</style>