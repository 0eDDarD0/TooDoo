<template>
    <div id="headForm" v-show="phoneForm">
        <div class="exit" v-show="phoneForm" @click="formWindow">X</div>
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
    <div id="headFormPhone" @click="formWindow">
        Nueva Nota
    </div>  
</template>

<script>

export default {
    name: 'headForm',

    data(){
        return {
            titulo : "",
            descripcion : "",
            prioridad : "3",
            phoneForm : true
        }
    },

    methods: {
        //EMITE UN EVENTO PARA LA CREACION DE UNA NOTA Y LIMPIA LOS INPUTS
        create(){
            this.$emit('create', {titulo : this.titulo, descripcion : this.descripcion, prioridad : this.prioridad});
            this.titulo = "";
            this.descripcion = "";
        },

        formWindow(){
            this.phoneForm = !this.phoneForm;
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

#headFormPhone,#headForm .exit{
    display: none;
}


@media screen and (max-width:650px){
    #headForm{
        position: fixed;

        flex-direction: column;
        justify-content: space-between;

        width: 75vw;
        height: 60vh;
        top: 10%;
    }
    #headForm input,#headForm select,#headForm .descripcion,#headForm button{
        width: 100%;
    }

    #headFormPhone{
        display: block;

        text-align: center;
    }
    #headForm .exit{
        display: block;
    }
}
</style>
