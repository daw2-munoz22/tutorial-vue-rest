<template>
    <div id="app" class="container">
        <div class="row">
            <div class="col-md-12 mt-2">
                <h1>Usuarios</h1>
            </div>
        </div>
        <div class="row">
            <div class="col-md-12">
                <!--acción postUsuario al evento crear-usuario-->
                <formulario-usuario @crear-usuario="postUsuario" />
                <!-- Este evento invocará al método putUsuario, actualizando los datos de usuario mediante la API. -->
                <tabla-usuarios :usuarios="usuarios" @eliminar-usuario="deleteUsuario" @actualizar-usuario="putUsuario" />
            </div>
        </div>
    </div>
</template>

<script>
//Importamos los componentes TablaUsuarios y FormularioUsuario
import TablaUsuarios from '/src/components/TablaUsuarios.vue';
import FormularioUsuario from '/src/components/FormularioUsuario.vue';

export default {
    name: "app",//nombre del componente
    data() {//Aquí se guardan los datos. En este caso, devuelve la variable usuarios (es una variable de estado), que es un Array 
        return {
            usuarios: [],
        }
    },
    components: { //Aqui se definen loscomponentes que vamos a utilizar
        TablaUsuarios,
        FormularioUsuario
    },
    methods: {//En este apartado, guardamos TODAS las funciones que tendá la aplicación
        async getUsuarios() { //Metodo para obtener la lista de usuarios
            try {
              //Obtenemos los datos mediante el await y después, los devuelve en formato JSON
                const response = await fetch('https://jsonplaceholder.typicode.com/users');
                this.usuarios = await response.json();
            } catch (error) {
                console.error(error);
            }
        },
        async postUsuario(usuario) {//Metódo para crear un usuario
            try {
                const response = await fetch('https://jsonplaceholder.typicode.com/users', {
                    method: 'POST', //definimos que el metodo es POST
                    body: JSON.stringify(usuario),//Enviamos los datos del usuario en el body de la petición y los convertimos en formato JSON
                    headers: { 'Content-type': 'application/json; charset=UTF-8' },//guardamos el contenido de la cabecera
                });
                
                const usuarioCreado = await response.json();//en usuarioCreado guarda el JSON
                this.usuarios = [...this.usuarios, usuarioCreado];//Unimos el array de usuarios con el objeto insertado
            } catch (error) {
                console.error(error);
            }
        },
        async putUsuario(usuario) {//Metodo para actualizar un usuario
            try {
                const response = await fetch(`https://jsonplaceholder.typicode.com/users/${usuario.id}`, {
                    method: 'PUT',//Metodo PUT (Update)
                    body: JSON.stringify(usuario),
                    headers: { 'Content-type': 'application/json; charset=UTF-8' },
                });
                
                const usuarioActualizado = await response.json();
                this.usuarios = this.usuarios.map(u => (u.id === usuario.id ? usuarioActualizado : u));//Enviamos el id del usuario que queremos actualizar en la URL, siguiendo así el estándard REST
                //Map: su objetivo es devolver un nuevo array donde cada uno de sus elementos será lo que devuelva la función callback por cada uno de los elementos del array original (En este caso, el array de usuarios)
            } catch (error) {
                console.error(error);
            }
        },
        async deleteUsuario(usuario) {//Metodo para borrar un usuario
            try {
                await fetch(`https://jsonplaceholder.typicode.com/users/${usuario.id}`, {
                    method: "DELETE"
                });
                
                this.usuarios= this.usuarios.filter(u => u.id !== usuario.id); //Con filter, generamos un Array vacío [] (array nuevo)
            } catch (error) {
                console.error(error);
            }
        },
    },//En este apartado, se ejecutará cuando se monte el componente, que en este caso ocurre al cargar la aplicación:
    mounted() {
        this.getUsuarios();
    }
};
</script>