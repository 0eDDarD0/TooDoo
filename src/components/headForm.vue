<template>
    <div id="headForm">
        Titulo:
        <input v-model="titulo">
        Descripción:
        <input class="descripcion" v-model="descripcion">
        Prioridad: 
        <select v-model="prioridad">
            <option value="1">Alta</option>
            <option value="2">Media</option>
            <option value="3">Baja</option>
        </select>
        <button @click="create">Crear</button>
    </div>
</template>

<script>

export default {
    name: 'headForm',

    data(){
        return {
            titulo : "",
            descripcion : "",
            prioridad : "3"
        }
    },

    methods: {
        //EMITE UN EVENTO PARA LA CREACION DE UNA NOTA Y LIMPIA LOS INPUTS
        create(){
            this.$emit('create', {titulo : this.titulo, descripcion : this.descripcion, prioridad : this.prioridad});
            this.titulo = "";
            this.descripcion = "";
        }
    }
}
</script>

<style>
#headForm{
    display: flex;
    flex-direction: row;
    justify-content: space-evenly;
}
#headForm .descripcion{
    width: 25%;
}
#headForm button{
    width: 5%;
}
#headForm select{
    width: 10%;
}

</style>
