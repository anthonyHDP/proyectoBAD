<template>
            <main class="main">
            <!-- Breadcrumb -->
            <ol class="breadcrumb">
                <li class="breadcrumb-item">Home</li>
                <li class="breadcrumb-item">Administrador</li>
                <li class="breadcrumb-item">Permisos de acceso</li>
                <li class="breadcrumb-item active">Roles</li>
            </ol>
            <div class="container-fluid">
                <!-- Ejemplo de tabla Listado -->
                <div class="card">
                    <div class="card-header">
                        <i class="fa fa-align-justify"></i> Roles
                        <button type="button" @click="abrirModal('rol', 'registrar')"  class="btn btn-success btn-nuevo">
                            <i class="icon-plus"></i> <span style="margin-left:2%">Nuevo</span>
                        </button>
                    </div>
                    <div class="card-body">
                        <div class="form-group row">
                            <div class="col-md-6">
                                <div class="input-group">
                                    <select class="form-control col-md-3" v-model="criterio">
                                      <option value="nombre">Nombre</option>
                                    </select>
                                    <input type="text" v-model="buscar" @keyup.enter="listarRol(1, buscar, criterio)" class="form-control" placeholder="Texto a buscar">
                                    <button type="submit" @click="listarRol(1, buscar, criterio)" class="btn btn-primary btn-buscar"><i class="fa fa-search"></i> Buscar</button>
                                </div>
                            </div>
                        </div>
                        <table class="table table-bordered table-striped table-sm">
                            <thead>
                                <tr>
                                    <th style="text-align: center">ID</th>
                                    <th>Nombre</th>
                                    <th>Privilegios</th>
                                    <th style="text-align: center">Opciones</th>
                                </tr>
                            </thead>
                            <tbody>
                                <tr v-for="rol in arrayRol" :key="rol.id_rol">
                                    <td v-text="rol.id_rol" style="text-align: center"></td>
                                    <td v-text="rol.nombre"></td>
                                    <td></td>
                                    <td style="text-align: center;">
                                        <button type="button" @click="abrirModalVincular(rol)" class="btn btn-info btn-sm btn-circle-text-white">
                                          <i class="icon-plus"></i>
                                        </button>
                                        <button type="button" @click="abrirModalVincular(rol)" class="btn btn-secondary btn-sm btn-circle-text-white">
                                          <i class="icon-minus"></i>
                                        </button>
                                        <button type="button" @click="abrirModal('rol', 'actualizar', rol)" class="btn btn-warning btn-sm btn-circle-text-white">
                                          <i class="icon-pencil"></i>
                                        </button>
                                        <button type="button" @click="abrirModalEliminar(rol)" class="btn btn-danger btn-sm btn-circle">
                                          <i class="icon-trash"></i>
                                        </button>
                                    </td>
                                </tr>                                
                            </tbody>
                        </table>
                        <nav>
                            <ul class="pagination">
                                <li class="page-item" v-if="pagination.current_page > 1">
                                    <a class="page-link" href="#" @click.prevent="cambiarPagina(pagination.current_page - 1,buscar, criterio)">Ant</a>
                                </li>
                                <li class="page-item" v-for="page in pagesNumber" :key="page" :class="[page == isActived ? 'active' : '']">
                                    <a class="page-link" href="#" @click.prevent="cambiarPagina(page,buscar, criterio)" v-text="page"></a>
                                </li>
                                <li class="page-item" v-if="pagination.current_page < pagination.last_page">
                                    <a class="page-link" href="#" @click.prevent="cambiarPagina(pagination.current_page + 1, buscar, criterio)">Sig</a>
                                </li>
                            </ul>
                        </nav>
                    </div>
                </div>
                <!-- Fin ejemplo de tabla Listado -->
            </div>
            <!--Inicio del modal agregar/actualizar-->
            <div class="modal fade" tabindex="-1" :class="{'mostrar' : modal}" role="dialog" aria-labelledby="myModalLabel" style="display: none;" aria-hidden="true">
                <div class="modal-dialog modal-primary" role="document" style="border-radius: 4px;">
                    <div class="modal-content">
                        <div class="modal-header">
                            <h4 class="modal-title" v-text="tituloModal"></h4>
                            <button type="button" class="close" @click="cerrarModal()" aria-label="Close">
                              <span aria-hidden="true" style="color:white">×</span>
                            </button>
                        </div>
                        <div class="modal-body" style="padding:4%">
                            <form action="" method="post" enctype="multipart/form-data" class="form-horizontal"> 
                                <div class="form-group">
                                    <label for="nombre">Nombre</label>
                                    <input type="text" v-model="nombre" class="form-control" placeholder="Ingrese el nombre del rol">
                                </div>
                                <div v-show="errorRol" class="form-group div-error">
                                    <div class="text-center text-error">
                                        <div v-for="error in errorMostrarMsjRol" :key="error" v-text="error">

                                        </div>
                                    </div>
                                </div>                               
                            </form>
                        </div>
                        <div class="modal-footer">
                            <button type="button" class="btn btn-secondary btn-circle-text-white" @click="cerrarModal()">Cerrar</button>
                            <button type="button" v-if="tipoAccion==1" @click="registrarRol()" class="btn btn-primary btn-circle">Guardar</button>
                            <button type="button" v-if="tipoAccion==2" @click="actualizarRol()"  class="btn btn-primary btn-circle">Actualizar</button>
                        </div>
                    </div>
                    <!-- /.modal-content -->
                </div>
                <!-- /.modal-dialog -->
            </div>
            <!-- Fin del modal -->
            <!-- Inicio del modal Eliminar -->
            <div class="modal fade" :class="{'mostrar' : modalEliminar}" tabindex="-1" role="dialog" aria-labelledby="myModalLabel" style="display: none;" aria-hidden="true">
                <div class="modal-dialog modal-danger" role="document">
                    <div class="modal-content">
                        <div class="modal-header">
                            <h4 class="modal-title">Eliminar Rol</h4>
                            <button type="button" class="close" @click="cerrarModalEliminar()" aria-label="Close">
                              <span aria-hidden="true" style="color:white">×</span>
                            </button>
                        </div>
                        <div class="modal-body">
                            <p>Estas seguro de eliminar el Rol: <span v-text="nombre"></span>? </p>
                        </div>
                        <div class="modal-footer">
                            <button type="button" class="btn btn-secondary btn-circle-text-white" @click="cerrarModalEliminar()">Cerrar</button>
                            <button type="button" class="btn btn-danger btn-circle" @click="eliminarRol(id_rol)">Eliminar</button>
                        </div>
                    </div>
                    <!-- /.modal-content -->
                </div>
                <!-- /.modal-dialog -->
            </div>
            <!-- Fin del modal Eliminar -->
        </main>
</template>

<script>
    export default {
        data (){
            return {
                id_rol: 0,
                nombre : '',
                arrayRol : [],
                modal : 0,
                tituloModal : '',
                tipoAccion : 0,
                errorRol: 0,
                errorMostrarMsjRol: [],
                pagination : {
                    'total' : 0,
                    'current_page' : 0,
                    'per_page' : 0,
                    'last_page' : 0,
                    'from' : 0,
                    'to' : 0,
                },
                offset : 3,
                criterio : 'nombre',
                buscar : '',
                modalEliminar: 0
            }
        },
        computed:{
            isActived: function(){
                return this.pagination.current_page;
            },
            //Calcula los elementos de la paginación
            pagesNumber: function() {
                if(!this.pagination.to) {
                    return [];
                }
                
                var from = this.pagination.current_page - this.offset; 
                if(from < 1) {
                    from = 1;
                }

                var to = from + (this.offset * 2); 
                if(to >= this.pagination.last_page){
                    to = this.pagination.last_page;
                }  

                var pagesArray = [];
                while(from <= to) {
                    pagesArray.push(from);
                    from++;
                }
                return pagesArray;             

            }
        },
        methods : {
            listarRol (page,buscar,criterio){
                let me=this;
                var url= '/rol?page=' + page + '&buscar='+ buscar + '&criterio='+ criterio;
                axios.get(url).then(function (response) {
                    var respuesta= response.data;
                    me.arrayRol = respuesta.roles.data;
                    me.pagination= respuesta.pagination;
                })
                .catch(function (error) {
                    console.log(error);
                });
            },
            cambiarPagina(page,buscar,criterio){
                let me = this;
                //Actualiza la página actual
                me.pagination.current_page = page;
                //Envia la petición para visualizar la data de esa página
                me.listarRol(page,buscar,criterio);
            },

            //ELIMINAR ROL
            eliminarRol(id_rol){
                let me=this;
                axios.put('/rol/eliminar', {
                    'id_rol': this.id_rol
                })
                .then(function (response) {
                    me.cerrarModalEliminar();
                    me.listarRol(1, '', 'nombre');
                }).catch(function (error) {
                    console.log(error);
                });
            },
            
            //ABRIR MODAL ELIMINAR
            abrirModalEliminar(data = []){
                this.modalEliminar = 1;
                this.nombre = data['nombre'];
                this.id_rol = data['id_rol'];
            },

            //CERRAR MODAL
            cerrarModalEliminar(){
                this.modalEliminar = 0;
                this.nombre = '';
                this.id_rol = 0;
            },

            //REGISTRAR OBJETO
            registrarRol(){
                if (this.validarRol()){
                    return;
                }

                let me=this;
                axios.post('/rol/registrar', {
                    'nombre': this.nombre,
                })
                .then(function (response) {
                    me.cerrarModal();
                    me.listarRol(1, '', 'nombre');
                }).catch(function (error) {
                    console.log(error);
                });
            },

            //ACTUALIZAR OBJETO
            actualizarRol(){
                if (this.validarRol()){
                    return;
                }

                let me=this;
                axios.put('/rol/actualizar', {
                    'nombre': this.nombre,
                    'id_rol': this.id_rol
                })
                .then(function (response) {
                    me.cerrarModal();
                    me.listarRol(1, '', 'nombre');
                }).catch(function (error) {
                    console.log(error);
                });
            },            

            //VALIDACION DE CAMPOS
            validarRol(){
                this.errorRol = 0;
                this.errorMostrarMsjRol = [];

                if (!this.nombre) this.errorMostrarMsjRol.push("El nombre del rol no puede estar vacío.");

                if (this.errorMostrarMsjRol.length) this.errorRol = 1;

                return this.errorRol;
            },

            //ABRIR MODAL REGISTRAR/ACTUALIZAR
            abrirModal(modelo, accion, data = []){
            switch(modelo)
            {
                case "rol":
                    {
                        switch(accion){
                            case 'registrar':
                                {
                                    this.modal = 1;
                                    this.nombre = '';
                                    this.tituloModal = 'Registrar Rol';
                                    this.tipoAccion = 1;
                                    break
                                }
                            case 'actualizar':
                                {
                                    this.modal = 1;
                                    this.nombre = data['nombre'];
                                    this.id_rol = data['id_rol'];
                                    this.tituloModal = 'Actualizar Rol';
                                    this.tipoAccion = 2;
                                    break
                                }
                        }
                    }
            }
        },

        //CERRAR MODAL
        cerrarModal(){
            this.modal = 0;
            this.nombre = '';
            this.tituloModal = '';
            this.errorMostrarMsjRol = [];
            this.errorRol = 0;
        },

        },
        mounted() {
            this.listarRol(1, this.buscar, this.criterio);
        }
    }
</script>
<style>    
    .modal-content{
        width: 100% !important;
        position: absolute !important;
    }
    .mostrar{
        display: list-item !important;
        opacity: 1 !important;
        position: absolute !important;
        background-color: #3c29297a !important;
    }
    .div-error{
        display: flex;
        justify-content: center;
    }
    .text-error{
        color: red !important;
        font-weight: bold;
    }
    .text-error{
        color: red !important;
        font-weight: normal !important;
        font-style:italic;
    }
    .btn-circle{
        border-radius: 4px;
    }
    .btn-circle-text-white{
        border-radius: 4px; color:white;
    }
    .btn-nuevo{
        margin-left:1%;
        margin-top:0.2% !important;
        border-radius: 6px; 
        color:white !important;
    }
    .btn-buscar{
        border-top-right-radius:4px;
        border-bottom-right-radius:4px;
    }
</style>
