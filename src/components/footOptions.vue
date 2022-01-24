<template>
    <div id="footOptions">
        <p>Tienes {{tamanio}} tareas, {{sinTerminar}} están pendientes</p>
        <span @click="todas">Todas</span>
        <span @click="pendientes">Pendientes</span>
        <span @click="terminadas">Terminadas</span>
        <span @click="clear">Limpiar terminadas</span>
        Búsqueda: <input type="text" v-model="buscar" @keyup="search">
    </div>
</template>

<script>

export default {
    name: 'App',

    props: {
        size : Number,
        notdone : Number
    },

    data(){
        return {
            buscar : ""
        }
    },

    computed: {
        //IMPRIME EL TAMAÑO DEL ARRAY DE NOTAS
        tamanio(){
            return this.size
        },
        //IMPRIME EL NUMERO DE NOTAS NO TERMINADAS
        sinTerminar(){
            return this.notdone;
        },
    },

    methods: {
        //FUNCIONES DE BOTONES DE CAMBIO DE MODO, SE ENCARGAN DE EMITIR EVENTOS
        todas(){
            this.$emit('modoTodas');
        },
        pendientes(){
            this.$emit('modoPendientes');
        },
        terminadas(){
            this.$emit('modoTerminadas');
        },
        search(){
            this.$emit('modoBusqueda', {clave : this.buscar});
        },

        clear(){
            this.$emit('clear');
        },
    }
}
</script>

<style>
#footOptions{
    display: flex;
    flex-direction: row;
    justify-content: space-evenly;
}

#footOptions span{
    text-decoration: underline;
    cursor: pointer;    
}
</style>
