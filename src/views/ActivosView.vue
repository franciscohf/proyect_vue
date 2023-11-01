<template>
  <div class="card">
    <div class="card-content">
      <form @submit.prevent="getAllList()">
        <h5>Buscar Activos</h5>
        <div class="row">
          <div class="col m10">
            <p>Nombre Activo: <input type="search" v-model="search" @search="getAllList()" /></p>
          </div>
          <div class="col m2">
            <button type="submit" class="waves-effect waves-light btn-small">buscar</button>
          </div>


        </div>

      </form>
    </div>
  </div>
  <div class="card">
    <div class="card-content">
      <h5>filtros</h5>
      <div class="input-field ">
        <select @change="filter('estado', $event.target.value)">
          <option value="" selected>Seleccione un estado</option>
          <option value="NUEVO">NUEVO</option>
          <option value="USADO">USADO</option>
          <option value="EN DESUSO">EN DESUSO</option>
        </select>
        <label>Estado</label>
      </div>

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
        <th>Tipo</th>
        <th>Marca</th>
        <th>Modelo</th>
        <th>Estado</th>
        <th>Area</th>
      </tr>
    </thead>

    <tbody>
      <tr v-for="item in items">
        <td>{{ item.id }}</td>
        <td>{{ item.tipo }}</td>
        <td>{{ item.marca }}</td>
        <td>{{ item.modelo }}</td>
        <td>{{ item.estado }}</td>
        <td>{{ item.area.nombre }}</td>
        <td>
          <a class="app-btn btn-small btn-floating btn-large waves-effect waves-light blue "><i class="material-icons"
              @click="getId(item.id), openForm()">edit</i></a>
          <a class="app-btn btn-small btn-floating btn-large waves-effect waves-light red"><i class="material-icons"
              @click="eliminar(item.id)">delete</i></a>
        </td>
      </tr>

    </tbody>
  </table>

  <div id="modalActivos" class="modal">
    <div class="modal-content">
      <h4 class="center-align">{{ idActivo == 0 ? 'Insertar Activo' : 'Actualizar Activo' }}</h4>
      <div class="row" v-if="idActivo == 0 ? false : true">
        <div class="col m4">
          <label>Codigo</label>
          <input type="text" disabled v-model="idActivo">
        </div>
      </div>
      <div class="row">
        <div class="col m4">
          <label>Tipo</label>
          <input type="text" style="text-transform:uppercase" v-model="payload.tipo">
        </div>
        <div class="col m4">
          <label>Marca</label>
          <input type="text" style="text-transform:uppercase" v-model="payload.marca">
        </div>
        <div class="col m4">
          <label>Modelo</label>
          <input type="text" style="text-transform:uppercase" v-model="payload.modelo">
        </div>
        <div class="col m4">
          <label>Estado</label>
          <select v-model="payload.estado">
            <option value="" selected>Seleccione un estado</option>
            <option value="NUEVO">NUEVO</option>
            <option value="USADO">USADO</option>
            <option value="EN DESUSO">EN DESUSO</option>
          </select>
        </div>
        <div class="col m4">
          <label>Area</label>
          <select v-model="payload.areaId">
            <option :value="0" selected>Seleccione una Area</option>
            <option :value="items.id" v-for="items in itemsAreas">{{ items.nombre }}</option>
          </select>
        </div>
      </div>
    </div>
    <div class="modal-footer">
      <button class="btn teal" @click="save()">{{ idActivo == 0 ? 'Guardar' : 'Actualizar' }}</button>
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
      itemsAreas: [],
      search: '',
      toFilter: '',
      modales: [],
      idActivo: 0,
      payload: {
        tipo: "",
        marca: "",
        modelo: "",
        estado: "",
        areaId: 1
      }

    }
  },
  methods: {
    openForm() {
      this.resetForm()
      var modal_activos = M.Modal.getInstance(document.querySelector('#modalActivos'))
      modal_activos.open()
    },
    closeForm() {
      var modal_activos = M.Modal.getInstance(document.querySelector('#modalActivos'))
      modal_activos.close()
    },
    resetForm() {
      this.idActivo = 0
      this.payload.id = 0
      this.payload.tipo = ""
      this.payload.marca = ""
      this.payload.modelo = ""
      this.payload.estado = ""
      this.payload.areaId = 0

    },
    filter(name, value) {
      this.toFilter = value === '' ? '' : '&' + name + '=' + value;
      this.getAllList();
    },
    getAllList() {

      this.axios({
        method: 'get',
        url: this.api + '/activos?_expand=area&q=' + this.search + this.toFilter
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
        url: this.api + '/activos/' + id
      }).
        then((response) => {
          this.idActivo = response.data.id
          this.payload = response.data;
        }).
        catch((error) => {
          console.log(error);
        })
    },
    save() {
   
      if (this.idActivo) {

        //actualizar
        this.axios({
          method: 'patch',
          url: this.api + '/activos/' + this.idActivo,
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

        this.axios({
          method: 'post',
          url: this.api + '/activos',
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
          url: this.api + '/activos/' + id
        }).
          then((response) => {
            this.getAllList();
          }).
          catch((error) => {
            console.log(error);
          });
      }
    },
    getListAreas() {

      this.axios({
        method: 'get',
        url: this.api + '/areas'
      }).
        then((response) => {
          this.itemsAreas = response.data;
          console.log("lista de areas=" + this.itemsAreas)
        }).
        catch((error) => {
          console.log(error);
        })
    },
  },
  mounted() {
    this.getAllList();
    this.getListAreas();
    var elems = document.querySelectorAll('.modal')
    this.modales = M.Modal.init(elems, null)


  },
  updated() {
    var select = document.querySelectorAll('select');
    var instances = M.FormSelect.init(select);
  }
}
</script>
  