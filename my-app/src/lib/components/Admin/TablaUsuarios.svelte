<script>
    import { onMount } from "svelte";

    let todos = {};
    let loading = true;
    let error = null;
    let mostrandoModalEdicion = false; // Variable para controlar la visibilidad

    onMount(async () => {
        try {
            console.log("2");
            const response = await fetch(
                "https://proyectomascotas.onrender.com/get_users",
            );
            if (!response.ok) throw new Error("Error al cargar los datos");
            const data = await response.json();
            todos = data.resultado;
            console.log(todos);

            setTimeout(() => {
                globalThis.$("#myTable").DataTable(); // Para convertrlo en datatable :D
            }, 0);
        } catch (e) {
            error = e.message;
        } finally {
            loading = false;
        }
    });

    var id = 1;
    var a = "";

    async function Ocultar() {
        mostrandoModalEdicion = false;
        const v_editar = document.getElementById("nav-listado");
        v_editar.setAttribute("class", "fade");

        let mostrar = document.getElementById("Mostrarusuario");
        mostrar.removeAttribute("class");

        location.reload();
    }

    var vid = 1;
    async function editar(id, a) {
        mostrandoModalEdicion = true;
        console.log("Editando a " + a);
        const v_editar = document.getElementById("nav-listado");
        v_editar.removeAttribute("class");
        console.log(v_editar);
        vid = id;
        console.log(Number.isInteger(vid));

        console.log(vid);
        let ocultar = document.getElementById("Mostrarusuario");
        ocultar.setAttribute("class", "fade");
        console.log(ocultar);

        const cambiar = ocultar.parentElement;
        console.log(cambiar);

        cambiar.insertBefore(v_editar, ocultar);
        console.log("NO Entra al try de buscar");

        try {
            console.log("Entra al try de buscar");

            const response = await fetch(
                `https://proyectomascotas.onrender.com/get_user/${vid}`,
                {
                    method: "GET",
                    headers: {
                        "Content-Type": "application/json",
                    },
                },
            );
            const data = await response.json();
            console.log(response);
            console.log(data);
            console.log("Buscando al usuario seleccionado");
            todos = data.apellido;
            console.log(todos);
            document.getElementById("nombres").value = data.nombre;
            document.getElementById("apellidos").value = data.apellido;
            document.getElementById("documento").value = data.documento;
            document.getElementById("telefono").value = data.telefono;
            document.getElementById("correo").value = data.email;
            console.log("verificando el estado: " + data.estado);
            const estado_v = data.estado ? "1" : "0"; //condicion ? valorSiVerdadero : valorSiFalso
            document.getElementById("estado").value = estado_v;

            const v_edit_nombre = document.getElementById("nombres");
            v_edit_nombre.removeAttribute("readonly");
            v_edit_nombre.focus();

            const v_edit_apellido = document.getElementById("apellidos");
            v_edit_apellido.removeAttribute("readonly");

            const v_edit_documento = document.getElementById("documento");
            v_edit_documento.removeAttribute("readonly");

            const v_edit_telefono = document.getElementById("telefono");
            v_edit_telefono.removeAttribute("readonly");

            const v_edit_usuario = document.getElementById("correo");
            v_edit_usuario.removeAttribute("readonly");

            const v_edit_estado = document.getElementById("estado");
            v_edit_estado.removeAttribute("readonly");
        } catch (e) {
            error = e.message;
        } finally {
            loading = false;
        }
    }

    async function actualizar() {
        console.log(vid);
        let vnombre = document.getElementById("nombres").value;
        let vapellidos = document.getElementById("apellidos").value;
        let vdocumento = document.getElementById("documento").value;
        let vtelefono = document.getElementById("telefono").value;
        let vcorreo = document.getElementById("correo").value;
        let vestado = document.getElementById("estado").value;

        // Convertir el valor de "estado" a booleano
        vestado = vestado === "1" ? true : false;

        console.log("ID DE ESTADO ENVIADO A LA BASE DE DATOS ES " + vestado);

        try {
            console.log("Entra al try de actualzar");

            const response = await fetch(
                `https://proyectomascotas.onrender.com/update_user_admin/${vid}`, // Incluye el user_id en la URL
                {
                    method: "PUT",
                    headers: {
                        "Content-Type": "application/json",
                    },
                    body: JSON.stringify({
                        email: vcorreo,
                        nombre: vnombre,
                        apellido: vapellidos,
                        documento: vdocumento,
                        telefono: vtelefono,
                        estado: vestado,
                    }),
                },
            );

            console.log("Actualizado");

            const Toast = Swal.mixin({
                toast: true,
                position: "top-end",
                showConfirmButton: false,
                timer: 3000,
                timerProgressBar: true,
                didOpen: (toast) => {
                    toast.onmouseenter = Swal.stopTimer;
                    toast.onmouseleave = Swal.resumeTimer;
                },
            });
            Toast.fire({
                icon: "success",
                iconColor: "white",
                color: "white",
                background: "#00bdff",
                title: "usuario actualizado con exito",
            });

            setTimeout(() => {
                const v_editar = document.getElementById("nav-listado");
                v_editar.setAttribute("class", "fade");

                let ocultar = document.getElementById("Mostrarusuario");
                ocultar.removeAttribute("class");

                const cambiar = v_editar.parentElement;
                cambiar.insertBefore(ocultar, v_editar);
                location.reload();
            }, 3000);
        } catch (e) {
            console.error("Error al actualizar el usuario:", e);
            Swal.fire({
                icon: "error",
                title: "Error",
                text: e.message,
            });
        } finally {
            loading = false;
        }
    }

    /*const serviceID = "service_acpug5r";
    const templateID = "template_bloqueouser";
    const apikey = "3bmpPn1S0SLhgotWj";*/

    async function desactivar(id /*nombre, email*/) {
        let vestado = 0;
        let vid = id;
        let estadoBooleano = vestado !== 0;
        //console.log("Correo" + email);
        try {
            const response = await fetch(
                `https://proyectomascotas.onrender.com/update_estado_user/${vid}`,
                {
                    method: "PUT",
                    headers: {
                        "Content-Type": "application/json",
                    },
                    body: JSON.stringify({
                        estado: estadoBooleano, // Envía el valor booleano
                    }),
                },
            );

            const data = await response.json();

            if (response.ok) {
                const Toast = Swal.mixin({
                    toast: true,
                    position: "bottom-end",
                    showConfirmButton: false,
                    timer: 3000,
                    timerProgressBar: true,
                    didOpen: (toast) => {
                        toast.onmouseenter = Swal.stopTimer;
                        toast.onmouseleave = Swal.resumeTimer;
                    },
                });
                Toast.fire({
                    icon: "error",
                    iconColor: "white",
                    color: "white",
                    background: "#ff4e4e",
                    title: "El Usuario a sido desactivado de manera exitosa",
                });
                //sendEmail()

                /*function sendEmail() {
                    emailjs.init(apikey);
                    emailjs
                        .send(serviceID, templateID, {
                            nombre: nombre,
                            email: email,
                        })
                        .then((result) => {
                            console.log("Correo enviado con exito");
                        })
                        .catch((error) => {
                            console.log(
                                "Error al enviar el correo:",
                                error.text,
                            );
                        });
                }*/

                setTimeout(() => {
                    location.reload();
                }, 3500);
            } else {
                alert(
                    "Error al desactivar: " + data.message ||
                        "Error desconocido",
                );
            }
        } catch (e) {
            alert("Error en la solicitud: " + e.message);
        }
    }

    async function activar(id) {
        let vestado = 1;
        let vid = id;

        // Convertir el valor a booleano
        let estadoBooleano = vestado === 1;

        try {
            const response = await fetch(
                `https://proyectomascotas.onrender.com/update_estado_user/${vid}`,
                {
                    method: "PUT",
                    headers: {
                        "Content-Type": "application/json",
                    },
                    body: JSON.stringify({
                        estado: estadoBooleano, // Envía el valor booleano
                    }),
                },
            );

            const data = await response.json();

            if (response.ok) {
                const Toast = Swal.mixin({
                    toast: true,
                    position: "bottom-end",
                    showConfirmButton: false,
                    timer: 3000,
                    timerProgressBar: true,
                    didOpen: (toast) => {
                        toast.onmouseenter = Swal.stopTimer;
                        toast.onmouseleave = Swal.resumeTimer;
                    },
                });
                Toast.fire({
                    icon: "success",
                    iconColor: "#000000",
                    color: "black",
                    background: "#76fa78",
                    title: "El usuario se ha activado de manera exitosa",
                });

                setTimeout(() => {
                    location.reload();
                }, 3500); //en milisegundos
            } else {
                alert(
                    "Error al Activar: " + data.message || "Error desconocido",
                );
            }
        } catch (e) {
            alert("Error en la solicitud: " + e.message);
        }
    }

    // Añadir esta función para manejar la búsqueda
    let todosFiltrados = [];
    $: todosFiltrados = filtrarUsuarios(todos, terminoBusqueda);

    let terminoBusqueda = "";

    function filtrarUsuarios(usuarios, termino) {
        if (!termino) return usuarios;

        const busqueda = termino.toLowerCase();
        return usuarios.filter(
            (user) =>
                user.nombre.toLowerCase().includes(busqueda) ||
                user.email.toLowerCase().includes(busqueda) ||
                user.documento.toLowerCase().includes(busqueda) ||
                user.apellido.toLowerCase().includes(busqueda),
        );
    }

    function buscarUsuario(termino) {
        terminoBusqueda = termino;
    }
</script>

<!-- Mantengo exactamente el mismo HTML y scripts, solo modifico los estilos -->
<div id="Mostrarusuario">
    <div class="mobile-users-container">
        <h2 class="mobile-users-title">
            <i class="bi bi-people-fill me-2"></i>USUARIOS
        </h2>

        <!-- Barra de búsqueda -->
        <div class="mobile-search-container">
            <i class="bi bi-search"></i>
            <input
                type="text"
                class="mobile-search-input"
                placeholder="Buscar por nombre, correo o documento..."
                on:input={(e) => buscarUsuario(e.target.value)}
            />
        </div>

        {#if loading}
            <div class="mobile-loading">
                <div class="spinner-border text-primary" role="status">
                    <span class="visually-hidden">Loading...</span>
                </div>
                <div>Cargando datos...</div>
            </div>
        {:else if error}
            <div class="alert alert-danger text-center">{error}</div>
        {:else if mostrandoModalEdicion}
            <!-- No mostrar nada (o podrías poner un mensaje) -->
        {:else if todosFiltrados.length === 0}
            <div class="no-results-message">
                <!-- Cambiado el nombre de la clase -->
                <i class="bi bi-exclamation-circle"></i>
                No se encontraron resultados
            </div>
        {:else}
            <div class="mobile-table-responsive">
                <table class="mobile-table" id="myTable">
                    <thead>
                        <tr>
                            <th>Usuario</th>
                            <th>Nombre</th>
                            <th>Apellido</th>
                            <th>Documento</th>
                            <th>Teléfono</th>
                            <th>Estado</th>
                            <th>Acciones</th>
                        </tr>
                    </thead>
                    <tbody>
                        {#each todosFiltrados as todo}
                            <!-- Cambiado a todosFiltrados -->
                            <tr>
                                <td data-label="Usuario">{todo.email}</td>
                                <td data-label="Nombre">{todo.nombre}</td>
                                <td data-label="Apellido">{todo.apellido}</td>
                                <td data-label="Documento">{todo.documento}</td>
                                <td data-label="Teléfono">{todo.telefono}</td>
                                <td data-label="Estado" class="text-center">
                                    <span
                                        class="mobile-badge {todo.estado
                                            ? 'active'
                                            : 'inactive'}"
                                    >
                                        {todo.estado ? "Activo" : "Desactivado"}
                                    </span>
                                </td>
                                <td data-label="Acciones" class="text-center">
                                    <div class="mobile-actions">
                                        <button
                                            class="mobile-btn edit"
                                            on:click={() => editar(todo.id)}
                                        >
                                            <i class="bi bi-pencil"></i>
                                            <span>Editar</span>
                                        </button>
                                        {#if todo.estado}
                                            <button
                                                class="mobile-btn deactivate"
                                                on:click={() =>
                                                    desactivar(todo.id)}
                                            >
                                                <i class="bi bi-lock"></i>
                                                <span>Desactivar</span>
                                            </button>
                                        {:else}
                                            <button
                                                class="mobile-btn activate"
                                                on:click={() =>
                                                    activar(todo.id)}
                                            >
                                                <i class="bi bi-unlock"></i>
                                                <span>Activar</span>
                                            </button>
                                        {/if}
                                    </div>
                                </td>
                            </tr>
                        {/each}
                    </tbody>
                </table>
            </div>
        {/if}
    </div>
</div>

<!-- Modal de Edición (estilos modificados pero estructura igual) -->
<div
    class="mobile-modal fade"
    id="nav-listado"
    role="tabpanel"
    aria-labelledby="nav-listado-tab"
>
    <div class="container text-center">
        <p class="text-warning"></p>
    </div>

    <div class="mobile-modal-card">
        <div class="mobile-modal-header">
            <h5><b>Editando Usuario</b></h5>
            <button
                class="mobile-close-btn"
                aria-label="Cerrar edición de usuario"
                on:click={() => Ocultar()}
            >
                <i class="bi bi-x-lg"></i>
            </button>
        </div>

        <div class="mobile-modal-body">
            <!-- Mantengo exactamente la misma estructura de inputs -->
            <div class="mobile-form-group">
                <label for="nombres"><b>Nombre:</b></label>
                <input
                    type="text"
                    id="nombres"
                    placeholder="Nombres"
                    maxlength="100"
                    readonly
                />
            </div>

            <div class="mobile-form-group">
                <label for="apellidos"><b>Apellido:</b></label>
                <input
                    type="text"
                    id="apellidos"
                    placeholder="Apellidos"
                    readonly
                />
            </div>

            <div class="mobile-form-group">
                <label for="documento"><b>Documento:</b></label>
                <input
                    type="text"
                    id="documento"
                    placeholder="Documento de identidad"
                    readonly
                />
            </div>

            <div class="mobile-form-group">
                <label for="telefono"><b>Teléfono:</b></label>
                <input
                    type="text"
                    id="telefono"
                    placeholder="Teléfono"
                    maxlength="20"
                    readonly
                />
            </div>

            <div class="mobile-form-group">
                <label for="correo"><b>Correo:</b></label>
                <input
                    type="text"
                    id="correo"
                    placeholder="Correo electrónico"
                    readonly
                />
            </div>

            <div class="mobile-form-group">
                <label for="estado"><b>Estado:</b></label>
                <select id="estado">
                    <option value="1">Activar</option>
                    <option value="0">Desactivar</option>
                </select>
            </div>

            <div class="mobile-modal-footer">
                <p>
                    ¡Al terminar de editar, haga clic en actualizar para guardar
                    los cambios!
                </p>
                <button on:click={actualizar} class="mobile-update-btn">
                    <b>Actualizar</b>
                </button>
            </div>
        </div>
    </div>
</div>

<link
    href="https://cdn.jsdelivr.net/npm/bootstrap-icons@1.10.0/font/bootstrap-icons.css"
    rel="stylesheet"
/>

<style>
    /* ESTILOS OPTIMIZADOS PARA MOBILE */
    .mobile-users-container {
        width: 100%;
        padding: 1.5rem;
        background: #fff;
        border-radius: 12px;
        box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
        margin: 1rem auto;
        max-width: 1200px;
        box-sizing: border-box; /* Añade esto para padding correcto */
    }

    .mobile-users-title {
        text-align: center;
        font-size: 1.5rem;
        color: #6e48aa;
        padding-bottom: 1rem;
        margin-bottom: 1rem;
        border-bottom: 2px solid #9d50bb;
        display: flex;
        align-items: center;
        justify-content: center;
    }

    /* Barra de búsqueda mejorada */
    .mobile-search-container {
        position: relative;
        margin: 1rem 0 1.5rem;
        width: 100%;
    }

    .mobile-search-container i {
        position: absolute;
        left: 1rem;
        top: 50%;
        transform: translateY(-50%);
        color: #6e48aa;
        font-size: 1.1rem;
    }

    .mobile-search-input {
        width: 100%;
        padding: 0.75rem 1rem 0.75rem 2.8rem;
        border: 2px solid #e0e0e0;
        border-radius: 8px;
        font-size: 1rem;
        transition: all 0.3s;
        box-sizing: border-box; /* Añade esto */
    }

    .mobile-search-input:focus {
        border-color: #9d50bb;
        box-shadow: 0 0 0 3px rgba(157, 80, 187, 0.2);
        outline: none;
    }

    /* Ajustes para responsive */
    @media (min-width: 768px) {
        .mobile-search-container {
            width: 70%;
            margin: 1rem auto 1.5rem;
        }
    }

    @media (min-width: 992px) {
        .mobile-search-container {
            width: 50%;
        }
    }

    /* Mensaje cuando no hay resultados (clase renombrada) */
    .no-results-message {
        text-align: center;
        padding: 2rem;
        color: #666;
        display: flex;
        flex-direction: column;
        align-items: center;
        gap: 0.5rem;
    }

    .no-results-message i {
        font-size: 2rem;
        color: #9d50bb;
    }

    @media (min-width: 992px) {
        .mobile-search-container {
            width: 50%;
            margin: 0 auto 1.5rem;
        }
    }

    /* Hasta aqui */

    .mobile-loading {
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;
        padding: 2rem;
        gap: 1rem;
    }

    .mobile-table-responsive {
        width: 100%;
        overflow-x: auto;
        -webkit-overflow-scrolling: touch;
    }

    .mobile-table {
        width: 100%;
        border-collapse: collapse;
    }

    .mobile-table thead {
        display: none;
    }

    .mobile-table tr {
        display: block;
        margin-bottom: 1rem;
        border-radius: 8px;
        box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
        overflow: hidden;
    }

    .mobile-table td {
        display: flex;
        justify-content: space-between;
        padding: 0.75rem;
        text-align: right;
        border-bottom: 1px solid #eee;
    }

    .mobile-table td:before {
        content: attr(data-label);
        font-weight: bold;
        text-align: left;
        margin-right: 1rem;
    }

    .mobile-table td:last-child {
        border-bottom: none;
    }

    .mobile-badge {
        padding: 0.25rem 0.75rem;
        border-radius: 20px;
        font-size: 0.875rem;
        font-weight: 600;
    }

    .mobile-badge.active {
        background: #e8f5e9;
        color: #2e7d32;
    }

    .mobile-badge.inactive {
        background: #ffebee;
        color: #c62828;
    }

    .mobile-actions {
        display: flex;
        gap: 0.5rem;
        justify-content: flex-end;
    }

    .mobile-btn {
        display: flex;
        align-items: center;
        gap: 0.25rem;
        padding: 0.375rem 0.75rem;
        border-radius: 20px;
        font-size: 0.875rem;
        border: 1px solid;
        background: transparent;
    }

    .mobile-btn.edit {
        border-color: #1565c0;
        color: #1565c0;
    }

    .mobile-btn.activate {
        border-color: #2e7d32;
        color: #2e7d32;
    }

    .mobile-btn.deactivate {
        border-color: #c62828;
        color: #c62828;
    }

    /* ESTILOS PARA EL MODAL EN MOBILE */
    .mobile-modal {
        position: fixed;
        top: 0;
        left: 0;
        right: 0;
        bottom: 0;
        display: flex;
        justify-content: center;
        align-items: center;
        z-index: 1050;
        opacity: 0;
        pointer-events: none;
        transition: opacity 0.3s ease;
    }

    .mobile-modal-card {
        background: #fff;
        border-radius: 12px;
        width: 90%;
        max-width: 500px;
        max-height: 90vh;
        overflow-y: auto;
    }

    .mobile-modal-header {
        padding: 1rem;
        border-bottom: 1px solid #eee;
        display: flex;
        justify-content: space-between;
        align-items: center;
    }

    .mobile-close-btn {
        background: none;
        border: none;
        font-size: 1.5rem;
        color: #666;
    }

    .mobile-modal-body {
        padding: 1rem;
    }

    .mobile-form-group {
        margin-bottom: 1rem;
    }

    .mobile-form-group label {
        display: block;
        margin-bottom: 0.5rem;
    }

    .mobile-form-group input,
    .mobile-form-group select {
        width: 100%;
        padding: 0.75rem;
        border: 1px solid #ddd;
        border-radius: 8px;
    }

    .mobile-form-group input:read-only {
        background: #f5f5f5;
    }

    .mobile-modal-footer {
        margin-top: 1.5rem;
        text-align: center;
    }

    .mobile-update-btn {
        background: #6e48aa;
        color: white;
        border: none;
        padding: 0.75rem 1.5rem;
        border-radius: 8px;
        font-weight: bold;
    }

    /* MEDIA QUERIES PARA VERSIÓN DE ESCRITORIO */
    @media (min-width: 992px) {
        .mobile-table thead {
            display: table-header-group;
        }

        .mobile-table tr {
            display: table-row;
            margin-bottom: 0;
            box-shadow: none;
        }

        .mobile-table td {
            display: table-cell;
            text-align: center;
            padding: 0.75rem;
        }

        .mobile-table td:before {
            display: none;
        }

        .mobile-table td:last-child {
            border-bottom: 1px solid #eee;
        }

        .mobile-modal-card {
            width: 50%;
        }
    }
</style>
