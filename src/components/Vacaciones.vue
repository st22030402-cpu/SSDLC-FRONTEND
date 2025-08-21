<template>
  <button @click="logout">Cerrar Sesión</button><br /><br />
  <button @click="incidencias">Incidencias</button><br /><br />

  <div>
    <h2>Módulo de Vacaciones</h2>

    <form @submit.prevent="solicitarVacacion">
      <label>Empleado ID:</label>
      <input type="number" v-model="nuevo.empleado_id" required />

      <label>Fecha Inicio:</label>
      <input type="date" v-model="nuevo.fecha_inicio" required />

      <label>Fecha Fin:</label>
      <input type="date" v-model="nuevo.fecha_fin" required />

      <label>Motivo:</label>
      <input type="text" v-model="nuevo.motivo" />

      <button type="submit">Solicitar Vacación</button>
    </form>

    <h3>Vacaciones Registradas</h3>
    <ul>
      <li v-for="vacacion in vacaciones" :key="vacacion.id">
        Empleado: {{ vacacion.empleado_id }}, Desde: {{ vacacion.fecha_inicio }}, Hasta:
        {{ vacacion.fecha_fin }}, Estado: {{ vacacion.estado }}
      </li>
    </ul>
  </div>
</template>

<script>
import axios from '@/api/axios'
import Swal from 'sweetalert2'
import { useUserStore } from '@/stores/user'

export default {
  data() {
    return {
      vacaciones: [],
      nuevo: {
        empleado_id: null,
        fecha_inicio: '',
        fecha_fin: '',
        motivo: '',
      },
    }
  },
  methods: {
    cargarVacaciones() {
      axios
        .get('/api/vacaciones')
        .then((response) => {
          this.vacaciones = response.data
        })
        .catch(console.error)
    },

    logout() {
      const userStore = useUserStore()
      userStore.logout()
      this.$router.push('/login')
    },

    incidencias() {
      this.$router.push('/incidences')
    },

    solicitarVacacion() {
      axios
        .post('/api/vacaciones', this.nuevo)
        .then(() => {
          this.cargarVacaciones()
          this.nuevo = { empleado_id: null, fecha_inicio: '', fecha_fin: '', motivo: '' }
          Swal.fire({
            icon: 'success',
            title: 'Solicitud exitosa',
            text: 'La vacación fue solicitada correctamente.',
          })
        })
        .catch((error) => {
          Swal.fire({
            icon: 'error',
            title: 'Error',
            text: error.response?.data?.message || 'Ocurrió un error al solicitar la vacación.',
          })
        })
    },
  },
  mounted() {
    this.cargarVacaciones()
  },
}
</script>
