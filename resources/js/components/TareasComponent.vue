<template>
    <div>
        <form @submit.prevent="editarNota(nota)" v-if="editarActivo">
      <h3>Editar nota</h3>
      <input type="text" class="form-control mb-2" 
        placeholder="Nombre de la nota" v-model="nota.nombre">
      <input type="text" class="form-control mb-2" 
        placeholder="Descripción de la nota" v-model="nota.descripcion">
      <button class="btn btn-success" type="submit">Guardar</button>
      <button class="btn btn-danger" type="submit" 
        @click="cancelarEdicion">Cancelar</button>
    </form>

         
         <form @submit.prevent="agregar" v-else>
             <h3>Agregar Notas</h3>
             <input type="text" placeholder="Nombre" class="form-control mb-2" v-model="nota.nombre">
            <input type="text" placeholder="Descripcion" class="form-control mb-2" v-model="nota.descripcion">
             <button class="btn btn-primary" type = "submit">Agregar</button>
         </form>
         
         <hr>
    <h3>Lista de notas:</h3>
    <ul class="list-group">
         <li class="list-group-item" 
            v-for="(item, index) in notas" :key="index" >
          <span class="badge badge-primary float-right">
            {{item.updated_at}}
          </span>
          <p>{{item.nombre}}</p>
          <p>{{item.descripcion}}</p>
          <p>
            <button class="btn btn-warning btn-sm" 
                @click="editarFormulario(item)">Editar</button>
            <button class="btn btn-danger btn-sm" 
                @click="eliminarNota(item, index)">Eliminar</button>
          </p>
        </li>
    </ul>
    </div>
</template>

<script>
export default {
    data(){
        return{
            notas : [],
            nota : {nombre : '', descripcion:''},
            editarActivo: false
        }
    },
     created(){
    axios.get('/notas').then(res=>{
      this.notas = res.data;
    })
  },

    methods:{
        agregar(){
           if(this.nota.nombre.trim() === '' || this.nota.descripcion.trim() === ''){
        alert('Debes completar todos los campos antes de guardar');
        return;
      }
           //console.log(this.nota.nombre, this.nota.descripcion);
           const params = {nombre:this.nota.nombre, descripcion: this.nota.descripcion };
           this.nota.nombre = '';
           this.nota.descripcion='';

           axios.post('/notas',params)
           .then(res=>{
             this.notas.push(res.data)
           })
        },
     eliminarNota(item, index){
       axios.delete(`/notas/${item.id}`)
          .then(()=>{
            this.notas.splice(index, 1);
          })
    } ,
    editarFormulario(item){
      this.nota.nombre = item.nombre;
      this.nota.descripcion = item.descripcion;
      this.nota.id = item.id;
      this.editarActivo = true;
    },
     editarNota(nota){
      const params = {nombre: nota.nombre, descripcion: nota.descripcion};
      axios.put(`/notas/${nota.id}`, params)
        .then(res=>{
          this.editarActivo = false;
          const index = this.notas.findIndex(item => item.id === nota.id);
          this.notas[index] = res.data;

this.nota = {nombre : '', descripcion:''}

    /*      axios.get('/notas').then(res=>{
      this.notas = res.data;
    })*/
        })
    },
     cancelarEdicion(){
      this.modoEditar = false;
      this.nota = {nombre: '', descripcion: ''};
    }
    }
}
</script>