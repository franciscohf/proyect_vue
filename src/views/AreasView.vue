<template>
  <div class="card">
    <div class="card-content">
      <form @submit.prevent="getAllList()">
        <h5>Buscar Area</h5>
        <div class="row">
          <div class="col m10">
            <p>Nombre Area: <input type="search" v-model="search" @search="getAllList()" /></p>
          </div>
          <div class="col m2">
            <button type="submit" class="waves-effect waves-light btn-small">buscar</button>
          </div>
         
         
        </div>

      </form>
    </div>
  </div>
  <div class="card-panel">
    <span class="black-text">
      <a class="waves-effect waves-light btn" @click="openForm()"><i class="material-icons left">add</i>Insertar</a>
    </span>
  </div>
  <table>
    <thead>
      <tr>
        <th>Codigo</th>
        <th>Nombre</th>
        <th>Encargado</th>
        <th>Nro. funcionarios</th>
      </tr>
    </thead>

    <tbody>
      <tr v-for="item in items">
        <td>{{ item.id }}</td>
        <td>{{ item.nombre }}</td>
        <td>{{ item.encargado }}</td>
        <td>{{ item.numero_funcionarios }}</td>
        <td>
          <a class="app-btn btn-small btn-floating btn-large waves-effect waves-light blue "><i class="material-icons"
              @click="getId(item.id), openForm()">edit</i></a>
          <a class="app-btn btn-small btn-floating btn-large waves-effect waves-light red"><i class="material-icons"
              @click="eliminar(item.id)">delete</i></a>
        </td>
      </tr>

    </tbody>
  </table>

  <div id="modalAreas" class="modal">
    <div class="modal-content">
      <h4 class="center-align">{{ idArea == 0 ? 'Insertar Area' : 'Actualizar Area' }}</h4>
      <div class="row" v-if="idArea == 0 ? false : true">
        <div class="col m4">
          <label>Codigo</label>
          <input type="text" disabled v-model="idArea">
        </div>
      </div>
      <div class="row">
        <div class="col m4">
          <label>Nombre</label>
          <input type="text" v-model="payload.nombre">
        </div>
        <div class="col m4">
          <label>Encargado</label>
          <input type="text" v-model="payload.encargado">
        </div>
        <div class="col m4">
          <label>Numero de funcionarios</label>
          <input type="text" v-model="payload.numero_funcionarios">
        </div>
      </div>
    </div>
    <div class="modal-footer">
      <button class="btn teal" @click="save()">{{ idArea == 0 ? 'Guardar' : 'Actualizar' }}</button>
    </div>
  </div>
</template>
  
<script>
// @ is an alias to /src

import M from "materialize-css";
export default {

  name: 'AreasView',
  data() {
    const api = process.env.VUE_APP_API;
    return {
      api,
      items: [],
      search: '',
      modales: [],
      idArea: 0,
      payload: {
        nombre: "",
        encargado: "",
        numero_funcionarios: 0,
      }

    }
  },
  methods: {
    openForm() {
      this.resetForm()
      var modal_areas = M.Modal.getInstance(document.querySelector('#modalAreas'))
      modal_areas.open()
    },
    closeForm() {
      var modal_areas = M.Modal.getInstance(document.querySelector('#modalAreas'))
      modal_areas.close()
    },
    resetForm() {
      this.idArea = 0
      this.payload.id = 0
      this.payload.nombre = ""
      this.payload.encargado = ""
      this.payload.numero_funcionarios = 0

    },
    getAllList() {

      this.axios({
        method: 'get',
        url: this.api + '/areas?q=' + this.search
      }).
        then((response) => {
          this.items = response.data;
        }).
        catch((error) => {
          console.log(error);
        })
    },
    getId(id) {
      this.axios({
        method: 'get',
        url: this.api + '/areas/' + id
      }).
        then((response) => {
          this.idArea = response.data.id
          this.payload = response.data;
        }).
        catch((error) => {
          console.log(error);
        })
    },
    save() {
      console.log(this.idArea);
      if (this.idArea) {
        console.log("ACTUALIZAR");
        //actualizar
        this.axios({
          method: 'patch',
          url: this.api + '/areas/' + this.idArea,
          data: this.payload
        }).
          then((response) => {
            this.getAllList();
            this.closeForm()
          }).
          catch((error) => {
            console.log(error);
          });
      } else {
        console.log("GUARDAR");
        //guardar
        console.log("envio=" + this.payload);
        this.axios({
          method: 'post',
          url: this.api + '/areas',
          data: this.payload
        }).
          then((response) => {
            this.resetForm();
            this.getAllList();
          }).
          catch((error) => {
            console.log(error);
          });

      }

    },
    eliminar(id) {
      if (confirm("Esta seguro de eliminar?.")) {
        this.axios({
          method: 'delete',
          url: this.api + '/areas/' + id
        }).
          then((response) => {
            this.getAllList();
          }).
          catch((error) => {
            console.log(error);
          });
      }
    },
  },
  mounted() {
    this.getAllList();
    var elems = document.querySelectorAll('.modal')
    this.modales = M.Modal.init(elems, null)

  }
}
</script>
  