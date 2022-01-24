<template>
    <div id="app">
        <!--CABECERA FORM--->
        <headForm @create="create" />


        <!--CONTENEDOR DE NOTAS-->
        <div class="container">
            <transition-group name="list">
            <!--FOR DE NOTAS-->
            <div v-for="(nota, index) in filtro" v-bind:key="index" v-bind:class="{terminada : filtro[index].done, pendiente : !filtro[index].done}">
                <!--TEXTO DE LA NOTA-->
                <div class="info"  v-bind:class="{terminada_info : filtro[index].done, pendiente_info : !filtro[index].done}">
                    <h3>{{nota.titulo}}</h3>
                    <p class="nota_text">{{nota.descripcion}}</p>
                    <p>Prioridad: {{prioridadStr(nota.id)}} 
                        <span @click="cambioPrioridad(nota.id, 1)">Alta</span>
                        <span @click="cambioPrioridad(nota.id, 2)">Media</span>
                        <span @click="cambioPrioridad(nota.id, 3)">Baja</span>
                    </p>
                    <p>{{nota.fecha}} / Hace {{diferenciaMinutos(nota.id)}} minutos</p>
                </div>
                <!--BOTONES DE LA NOTA-->
                <div class="botones">
                    <div @click="terminar(nota.id)">O</div>
                    <div @click="eliminar(nota.id)">X</div>
                </div>
            </div>
            </transition-group>
        </div>


        <!--PIE DE OPCIONES-->
        <footOptions :size="notas.length" :notdone="sinTerminar" @modoTodas='todas' @modoPendientes='pendientes' @modoTerminadas='terminadas' @modoBusqueda='busqueda' @clear='limpiar' />
    </div>
</template>

<script>
import headForm from './components/headForm.vue'
import footOptions from './components/footOptions.vue'

//PARA SACAR CADA MES
const mes = ['enero', 'febrero', 'marzo', 'abril', 'mayo', 'junio', 'julio', 'agosto', 'septiembre', 'octubre', 'noviembre', 'diciembre'];

export default {
    name: 'App',


    components: {
        headForm,
        footOptions
    },


    data(){
        return {
            notas : [],
            id : 0,
            modo : 'todo',
            clave : ''
        }
    },


    beforeMount(){
        if(JSON.parse(localStorage.getItem('toodoo'))){
            //SI EXISTE UN LOCALSTORAGE LO RECOGE
            this.notas = JSON.parse(localStorage.getItem('toodoo'));
            if(this.notas.length){
                //PARA INCIALIZAR EL CONTADOR DE IDS RECOGE LA NOTA CON ID MAS ALTA Y LE SUMA 1
                let aux = this.notas.sort((a, b)=>{
                    return b.id - a.id
                });
                this.id = aux[0].id + 1;
            }
        }
    },


    methods: {
        //INTRODUCE UNA NOTA NUEVA EN EL ARRAY
        create(data){
            let d = new Date()
            this.notas.push({titulo : data.titulo,
                            descripcion : data.descripcion,
                            prioridad : data.prioridad,
                            fecha : d.getDate() + " - " + mes[d.getMonth()] + " - " + d.getFullYear(),
                            id : this.id,
                            done : false,
                            minutos : d.getTime()
            });

            this.id++;

            localStorage.setItem('toodoo', JSON.stringify(this.notas));
        },


        //ELIMINA DEL ARRAY LA NOTA SELECCIONADA
        eliminar(id){
            //ENCONTRAMOS Y ELIMINAMOS DE LOS DATOS
            for(let i = 0 ; i < this.notas.length ; i++){
                if(this.notas[i].id == id)
                    this.notas.splice(i, 1);
            }

            localStorage.setItem('toodoo', JSON.stringify(this.notas));
        },

        //CAMBIA EL ESTADO DONE DE LA NOTA SELECCIONADA
        terminar(id){
            //ENCONTRAMOS Y CAMBIAMOS EL DATO DONE
            let n = this.notas.filter(function(nota){
                return nota.id == id;
            });
            n[0].done = !n[0].done;
            
            localStorage.setItem('toodoo', JSON.stringify(this.notas));
        },


        //BORRA TODAS LAS NOTAS CON ESTADO DONE == TRUE
        limpiar(){
            this.notas = this.notas.filter(function(nota){
                return !nota.done;
            });

            localStorage.setItem('toodoo', JSON.stringify(this.notas));
        },


        //DEVUELVE UN STRING SEGUN LA PRIORIDAD DE LA NOTA
        prioridadStr(id){
            //RECOGEMOS LA NOTA CON EL ID DADO
            let n = this.notas.filter((nota)=>{
                return nota.id == id;
            });

            if(n[0].prioridad == 1){
                return "Alta";
            }else if(n[0].prioridad == 2){
                return "Media";
            }else{
                return "Baja";
            }
        },


        //CAMBIA LA PRIORIDAD PARA LA NOTA SELECCIONADA
        cambioPrioridad(id, p){
            let n = this.notas.filter((nota)=>{
                return nota.id == id;
            });
            n[0].prioridad = p;
        },


        //DEVUELVE LOS MINUTOS PASADOS DESDE LA CREACION DE LA NOTA
        diferenciaMinutos(id){
            let n = this.notas.filter((nota)=>{
                return nota.id == id;
            });
            let d = new Date();
            return Math.trunc((d.getTime() - n[0].minutos) / 60000);
        },


        //METODOS PARA BOTONES DE CAMBIO DE MODO
        todas(){
            this.modo = 'todo';
        },
        pendientes(){
            this.modo = 'pendiente';
        },
        terminadas(){
            this.modo = 'terminada';
        },
        busqueda(word){
            console.log(word);
            this.modo = 'busqueda';
            this.clave = word.clave;
        }
    },


    computed: {
        //DEVUELVE EL TAMAÑO DEL ARRAY
        tamanio(){
            return this.notas.length
        },
        
        //DEVUELVE EL NUMERO DE NOTAS NO TERMINADAS
        sinTerminar(){
            let cont = 0;
            for(let i = 0 ; i < this.notas.length ; i++){
                if(!this.notas[i].done){
                    cont++;
                }
            }
            return cont;
        },


        //EL ARRAY FINAL A RENDERIZAR, RECOGE LAS NOTAS SEGUN EL MODO Y LAS ORDENA SEGUN PRIORIDAD
        filtro(){
            let nuevo = [];

            //SEGUN EL MODO RECOGERA CIERTAS NOTAS DEL ARRAY
            if(this.modo == 'pendiente'){
                nuevo = this.notas.filter(function(nota){
                    return !nota.done;
                });
            }else if(this.modo == 'terminada'){
                nuevo = this.notas.filter(function(nota){
                    return nota.done;
                });
            }else if(this.modo == 'busqueda'){
                for(let i = 0 ; i < this.notas.length ; i++){
                    if(this.notas[i].titulo.includes(this.clave) || this.notas[i].descripcion.includes(this.clave))
                        nuevo.push(this.notas[i]);
                }
            }else{
                nuevo = this.notas;
            }

            //DEVUELVE EL ARRAY ORDENADO SEGUN PRIORIDAD
            return nuevo.sort((a, b)=>{
                return a.prioridad - b.prioridad;
            });
        }
    }
}
</script>

<style>
/*  ESTILOS GENERALES  */
@font-face {
    font-family: Comfortaa;
    src: url('./assets/fonts/Comfortaa.ttf');
}
*{
    padding: 0;
    margin: 0;

    font-family: Comfortaa;
}
#app{
    height: 100vh;

    background-image: linear-gradient(to top right, rgb(16, 0, 165), rgb(255, 0, 0));
    background-repeat: no-repeat;

    display: flex;
    flex-direction: column;
    justify-content: space-evenly;
}
#app > div{
    background-color: rgb(44, 39, 61);
    color: white;

    border-radius: 20px;

    padding: 15px;
    margin: 10px;
}
button{
    padding: 2px;
}


/*  ESTILOS DEL CONTENEDOR  */
.container{
    height: 68vh;

    display: flex;
    flex-direction: column;
    flex-wrap: wrap;
    overflow: auto;
}
.container div{
    margin: 5px;
    padding: 10px;

    border-radius: 20px;

    display: flex;
    flex-direction: row;
    justify-content: space-between;
}
.container .info,.container .botones{
    display: flex;
    flex-direction: column;
}
.container .nota_text{
    max-width: 30ch;
    word-wrap: break-word;
}
.container span{
    margin-left: 10px;
    padding: 3px;

    cursor: pointer;
    border: 2px solid;
    border-radius: 10px;
}
.container .botones{
    text-decoration: none;
    cursor: pointer;
}

.list-enter-active, 
.list-leave-active {
  transition: all 0.2s ease;
}
.list-enter-from,
.list-leave-to {
  opacity: 0;
  transform: translateY(30px);
}

/* ESTILO DE NOTA DINAMICO */
.terminada{
    color: black;

    background-color: palegreen;
}
.terminada_info{
    text-decoration: line-through;
}
.pendiente{
    color: white;

    background-color: palevioletred;
}
.pendiente_info{
    text-decoration: none;
}
</style>
