<template>
 <div>
  <!-- Content Header (Page header) -->
    <div class="content-header bg-light shadow-sm">
      <div class="container">
        <div class="row mb-2">
          <div class="col-sm-6">
            <h1 class="m-0">  Normatividad <small>Regional</small></h1>
          </div><!-- /.col -->
          <div class="col-sm-6"><!-- {{ info.nom_pagina }} -->
            <ol class="breadcrumb float-sm-right">
              <li class="breadcrumb-item"><router-link  :to="'/'">Inicio</router-link></li>
              <li class="breadcrumb-item">Normatividad regional</li>
              <!-- <li class="breadcrumb-item active">Top Navigation</li> -->
            </ol>
          </div><!-- /.col -->
        </div><!-- /.row -->
      </div><!-- /.container-fluid -->
    </div>
    <!-- /.content-header -->
   <div class="container pt-3 "  >
    
      <div class="row">
	      	
          <div class="card">
          
          	      	<div class="card-body">
                      <div class="form-group row">
                        <div class="col-sm-4">
                          <label for="">Tipo documento</label>
                          <select name="" id="" class="form-control" v-model="textdoc">
                            <option value="s">Todos</option>
                            <option v-for="td in datatipodoc" :values="td.indreg_tipo" v-if="td.indreg_tipo!='002o'">{{td.tip_description}}</option>
                          </select>
                        </div>
                        <div class="col-sm-2">
                          <label for="">Año</label>
                          <select name="" id="" class="form-control" v-model="anio">
                            <option value="0">Todo</option>
                            <option  v-for="fec in datfecha" :value="fec">{{fec}}</option>
                          </select>
                        </div>
                        <div class="col-sm-6">
                          <div class="input-group mt-4">
                          <input type="search" class="form-control form-control-lg" placeholder="Ingrese su búsqueda" v-model="buscar"  @keyup="getResults(paginaslista)">
                          <div class="input-group-append">
                              <button type="submit" class="btn btn-lg btn-primary" @click.prevent="getResults(paginaslista)">
                                  <i class="fa fa-search"></i>
                              </button>
                              <button class="btn btn-info" @click="limpiar">
                              <i class="fa-solid fa-rotate"></i>
                              </button>
                          </div>
                        </div>
                        </div>
                      </div>                      
                        
                  
                        <div class="form-group mt-3">
                        <table class="table table-borderless table-sm " style="">
                          <thead>
                            <tr>
                              <th>Documentos</th>                    
                              <th>Archivos</th>
                            </tr>
                          </thead>
                          <tbody>
                            <tr v-for="list in datanormatividad.data" :key="list.idregulation" class="border ">
                             <td >
                              <table width="100%" style="border:1px; padding:0px; margin:0px; ">
                               <tr>
                                <td width="12%"><strong>Doc:</strong></td><td>{{ list.reg_type }}({{list.reg_title}})</td>
                               </tr>
                               <tr>
                                <td><strong>Fecha:</strong></td> <td>{{list.reg_date_start}}</td>                              
                              </tr>
                              <tr>
                                <td><strong>Descripción:</strong></td><td>{{list.reg_description}}</td>                              
                              </tr>
                             
                             </table>
                             </td>
                             <td class="border-left">
                              <div v-html="archivos(list.reg_files,list.idregulation)" class="text-center">
                                
                              </div>
                              
                             </td>
                              
                            </tr>
                          </tbody>
                        </table>
                        </div>
                    </div>

                    <div class="card-footer clearfix">
                    <pagination :data="datanormatividad" :limit="1" @pagination-change-page="getResults"></pagination>
                    </div>
	      		
            
          </div>


	      </div>
      
    </div>
  
    
 </div>
</template>

<script>
import axios from 'axios';
import LaravelVuePagination from 'laravel-vue-pagination';
// Vue.component('pagination', pagination);


 export default {
   components: {
        'Pagination': LaravelVuePagination
    },
   name: '',
   data () {
     return {
      datanormatividad: {},
      datatipodoc:{},
      textdoc:'s',
      anio:0,
      buscar:'',
      paginaslista:1,
      datfecha:[],
      }
   },
   created () {
    var today = new Date();
    var year = today.getFullYear();
    var incr=0;

    for(var i=year;i>=1986;i--)
    {
      
      this.datfecha[incr]=i
      incr++;
    }
   },
   mounted () { 
    //this.buscador();
    // if(!this.buscar)
    //  {this.getResults();} 
     this.getResults();   
   },
   methods: {
    
        getResults(page) {
                          if (typeof page === "undefined") {
                            var pag=1;
                          }
                          else{
                            var pag=page;
                          }
                          this.paginaslista=pag
                           var url='/api/normatividad/'+this.textdoc+'/'+this.anio+'/'+this.buscar+'?page=' + pag;
                          
                          axios
                            .get(url)
                            .then(response => {
                              this.datanormatividad = response.data.normatividad;
                              this.datatipodoc=response.data.tipodoc;

                              // this.buscar=''
                              // console.log(this.datanormatividad);
                            });
                         },
      limpiar()
      {
        this.buscar='';
        this.textdoc='s';
        this.anio=0;
        this.getResults(); 
      },
      recorta_cad(texto)
      {
        var substr=texto.substr(7);
        return substr;
      },
      archivos(objeto,idreg)
      {
        var archivos=JSON.parse(objeto);
        var cantidad=archivos.length;
        var enlace='<span class="badge badge-dark right">'+cantidad+' Archivos</span>';
        for(var i=0;i<cantidad;i++)
        {
          enlace+='<a href="http://data.regionhuanuco.gob.pe/regulations/?reg='+idreg+'&tomo='+i+'"><span class="mailbox-attachment-icon" style="font-size: 25px !important; padding: 3px !important; color:#ff0909;"><i class="far fa-file-pdf"></i></span></a><p class="text-center">'+archivos[i].tamanio+' MB</p>';

        }

        return enlace;
      },
      
      

   }
 }
</script>

<style lang="css" scoped>

</style>
