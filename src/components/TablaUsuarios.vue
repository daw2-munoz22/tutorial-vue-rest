<!--Este es el componente TablaUsuarios-->
<template>
<div id="tabla-usuarios">
    <div v-if="!usuarios.length" class="alert alert-info" role="alert">
        No se han agregado usuarios
    </div>
    <table class="table">
        <thead>
            <tr>
                <th>Nombre</th>
                <th>Email</th>
                <th>Acciones</th>
            </tr>
        </thead>
        <tbody>
            <!--
                Usar un bucle v-for de Vue para recorrer el array de usuarios, que el componente acepta como una propiedad. 
                Mostramos únicamente dos columnas; una para el nombre «name» del usuario y otra para su email. 
                Usamos una condición v-if para mostrar una advertencia en caso de que no existan usuarios
            -->
            <tr v-for="usuario in usuarios" :key="usuario.id">
                <td v-if="editando === usuario.id">
                    <input type="text" class="form-control" v-model="usuario.name" />
                </td>
                <td v-else>
                    {{ usuario.name}}
                </td>
                <td v-if="editando === usuario.id">
                    <input type="email" class="form-control" v-model="usuario.email" />
                </td>
                <td v-else>
                    {{ usuario.email}}
                </td>
                <!-- Para declarar la acción hemos usado el atributo @ seguido del nombre del evento -->
                <td v-if="editando === usuario.id">
                    <button class="btn btn-success" @click="guardarUsuario(usuario)">💾 Guardar</button>
                    <button class="btn btn-secondary ml-2" @click="cancelarEdicion(usuario)">❌ Cancelar</button>
                </td>
                <td v-else>
                    <button class="btn btn-info" @click="editarUsuario(usuario)">✏️ Editar</button>
                    <!--
                        El botón emitirá el evento eliminar-usuario a la aplicación, de modo ésta pueda eliminar 
                        al usuario tanto del servidor como del array de usuarios. Los eventos se declaran usando la función $emit, 
                        seguido del nombre del evento y de los datos a enviar. 
                    -->
                    <button class="btn btn-danger ml-2" @click="$emit('eliminar-usuario', usuario)">🗑️ Eliminar</button>
                </td>
            </tr>
        </tbody>
    </table>
</div>
</template>
<script>
  export default {
    name: 'tabla-usuarios',
    props: {//definimos la propiedad usuarios como si fuera un Array
       usuarios: Array,
    },
    data() {
        return {
            editando: null,
        }
    },
    methods: {
        // Cuando se ejecuta el método editarUsuario entramos en modo edición, guardando los datos del usuario que se está editando en la variable de estado usuarioEditado, que usamos a modo de caché.
        editarUsuario(usuario) {
            this.usuarioEditado = Object.assign({}, usuario);
            this.editando = usuario.id;
        },
        guardarUsuario(usuario) {
            if (!usuario.name.length || !usuario.email.length) {
                return;  
            }
             // emitimos el evento actualizar-usuario cuando se ejecuta el método guardarUsuario
            this.$emit('actualizar-usuario', usuario);
            this.editando = null;
        },
        //  En caso de cancelar la edición, el método cancelarEdición restaurará los datos del usuario editado usando esta variable de estado.
        cancelarEdicion(usuario) {
            Object.assign(usuario, this.usuarioEditado);
            this.editando = null;
        }
    }
  }
</script>