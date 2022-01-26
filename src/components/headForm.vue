<template>
    <div id="headForm" v-show="phoneForm">
        <div class="exit" v-show="phoneForm" @click="formWindow">X</div>
        Titulo:
        <input v-model="titulo" @keyup.enter="create">
        Descripción:
        <input class="descripcion" v-model="descripcion" @keyup.enter="create">
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
            if(this.titulo){
                this.$emit('create', {titulo : this.titulo, descripcion : this.descripcion, prioridad : this.prioridad});
                this.titulo = "";
                this.descripcion = "";
            }
        },

        formWindow(){
            this.phoneForm = !this.phoneForm;
        },

    }
}
</script>

<style>
#headForm{
    display: flex;
    flex-direction: row;
    justify-content: space-evenly;
    align-items: center;
}

#headForm .descripcion{
    width: 25%;
}
#headForm select{
    width: 10%;
    cursor: pointer;
}

#headForm button{
    width: 5%;
    cursor: pointer;
}
#headForm button:hover{
    animation-name: crearHover;
    animation-duration: 0.3s;
    animation-fill-mode: forwards;
}
@keyframes crearHover{
    from{
        background-color: rgb(44, 39, 61);
        color: white;
    }
    to{
        color: rgb(44, 39, 61);
        background-color: white;
    }
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
