<script setup>
import { onMounted, ref } from "@vue/runtime-core"
import axios from 'axios'
const dataVentas = ref([]);
const dataDetalle = ref([]);
const visible = ref(false);

const buscarfecha = () => {

  let fecha = document.getElementById('fecha').value
  if(fecha !== ''){

    axios
       .get('http://localhost:8080/api/ventas/buscarfecha',{
       params: {
     fecha
   }
       })
       
       .then(response => {
         dataVentas.value = response.data
       })
  }
}
const verdetalle = idventa => {
  axios
      .get(`http://localhost:8080/api/ventas/${idventa}`)
      .then(response => {
        let data = response.data
        document.getElementById('datanombre').value = data.cliente.nombre
        document.getElementById('dataapellido').value = data.cliente.apellido
        document.getElementById('datadni').value = data.cliente.dni
        document.getElementById('datatelefono').value = data.cliente.telefono
        document.getElementById('dataemail').value = data.cliente.email
        document.getElementById('datafecha').value = data.fecha
        document.getElementById('datatotal').innerHTML = `S/. ${data.total}`

        dataDetalle.value = data.listadetalleventa
        visible.value = true
      })
}
const hidemodal = () => {
  visible.value = false
  dataDetalle.value = []
  }
onMounted(() =>{
  axios
      .get('http://localhost:8080/api/ventas')
      .then(response => {
        dataVentas.value = response.data
      })
} )
</script>

<style scoped>
.show{
  display: block;
}
</style>

<template>
<div class="input-group mb-3">
  <button @click="buscarfecha" class="btn btn-outline-secondary" type="button" id="button-addon1">Buscar</button>
  <input id="fecha" type="date" class="form-control" placeholder="" aria-label="Example text with button addon" aria-describedby="button-addon1">
</div>
      <table class="table col-12">
  <thead>
    <tr>
      <th scope="col">#</th>
      <th scope="col">Nombre</th>
      <th scope="col">Fecha</th>
      <th scope="col">Total</th>
      <th scope="col">Detalle</th>
    </tr>
  </thead>
  <tbody>
    <tr v-for="(element, index) in dataVentas" :key="element.idventa">
      <th scope="row">{{index}}</th>
      <td>{{element.cliente.nombre}}</td>
      <td>{{element.fecha}}</td>
      <td>{{element.total}}</td>
      <td>
        <button @click="verdetalle(element.idventa)" type="button" class="btn btn-link">Ver Detalle</button>
      </td>
    </tr>
  </tbody>
</table>



<div class="modal" v-bind:class="{ show: visible}" id="exampleModal" tabindex="-1" aria-labelledby="exampleModalLabel" aria-hidden="true">
  <div class="modal-dialog modal-lg">
    <div class="modal-content">
      <div class="modal-header">
        <h5 class="modal-title" id="exampleModalLabel">Modal title</h5>
        <button @click="hidemodal" type="button" class="btn-close" aria-label="Close"></button>
      </div>
      <div class="modal-body">
        <div class="row">
          <div class="col-4">
            <div class="mb-3">
              <label for="datanombre" class="form-label">Nombre</label>
              <input readonly type="text" class="form-control" id="datanombre">
            </div>
          </div>
          <div class="col-4">
            <div class="mb-3">
              <label for="dataapellido" class="form-label">Apellido</label>
              <input readonly type="text" class="form-control" id="dataapellido">
            </div>
          </div>
          <div class="col-4">
            <div class="mb-3">
              <label for="datadni" class="form-label">DNI</label>
              <input readonly type="text" class="form-control" id="datadni">
            </div>
          </div>
        </div>
        
        <div class="row">
          <div class="col-4">
            <div class="mb-3">
              <label for="datatelefono" class="form-label">Telefono</label>
              <input readonly type="text" class="form-control" id="datatelefono">
            </div>
          </div>
          <div class="col-4">
            <div class="mb-3">
              <label for="dataemail" class="form-label">Email</label>
              <input readonly type="text" class="form-control" id="dataemail">
            </div>
          </div>
        </div>


        <table class="table col-12">
  <thead>
    <tr>
      <th scope="col">Producto</th>
      <th scope="col">Precio Unitario</th>
      <th scope="col">Cantidad</th>
      <th scope="col">Subtotal</th>
    </tr>
  </thead>
  <tbody>
    <tr v-for="(element) in dataDetalle" :key="element.iddetalleventa">
      <td>{{element.producto.nombreproducto}}</td>
      <td>{{element.producto.precio}}</td>
      <td>{{element.cantidad}}</td>
      <td>{{element.cantidad * element.producto.precio}}</td>
    </tr>
  </tbody>
</table>

          <div class="row">
            <div class="col-6">
               <div class="mb-3">
              <label for="datafecha" class="form-label">Fecha</label>
              <input readonly type="text" class="form-control" id="datafecha">
            </div>
            </div>
            <div class="col-6">
              Total : <span id="datatotal"></span>
            </div>
          </div>
           
      </div>
      <div class="modal-footer">
        <button type="button" class="btn btn-secondary" @click="hidemodal">Close</button>
      </div>
    </div>
  </div>
</div>
</template>
