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
                "https://proyectomascotas.onrender.com/get_discapacitadosVCOMPLETOS",
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

        let mostrar = document.getElementById("MostrarDiscapacitado");
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
        let ocultar = document.getElementById("MostrarDiscapacitado");
        ocultar.setAttribute("class", "fade");
        console.log(ocultar);

        const cambiar = ocultar.parentElement;
        console.log(cambiar);

        cambiar.insertBefore(v_editar, ocultar);
        console.log("NO Entra al try de buscar");

        try {
            console.log("Entra al try de buscar");

            const response = await fetch(
                `https://proyectomascotas.onrender.com/get_discapacitadoV/${vid}`,
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
            todos = data.nombre;
            console.log(todos);
            document.getElementById("nombres").value = data.nombre;

            document.getElementById("genero").value =
                data.id_genero_discapacitado;

            document.getElementById("tipo_ceguera").value =
                data.id_tipo_ceguera;

            console.log("verificando el estado: " + data.estado);

            const estado_v = data.estado ? "1" : "0"; //condicion ? valorSiVerdadero : valorSiFalso

            document.getElementById("estado").value = estado_v;

            const v_edit_nombre = document.getElementById("nombres");
            v_edit_nombre.removeAttribute("readonly");
            v_edit_nombre.focus();

            const v_edit_genero = document.getElementById("genero");
            v_edit_genero.removeAttribute("readonly");

            const v_edit_tipo_ceguera = document.getElementById("tipo_ceguera");
            v_edit_tipo_ceguera.removeAttribute("readonly");

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
        let vgenero = document.getElementById("genero").value;
        let vtipoceguera = document.getElementById("tipo_ceguera").value;
        let vestado = document.getElementById("estado").value;

        console.log("ID DE ESTADO ENVIADO A LA BASE DE DATOS ES " + vestado);

        try {
            console.log("Entra al try de actualzar");

            const response = await fetch(
                `https://proyectomascotas.onrender.com/update_user/${vid}`, // Incluye el user_id en la URL
                {
                    method: "PUT",
                    headers: {
                        "Content-Type": "application/json",
                    },
                    body: JSON.stringify({
                        nombre: vnombre,
                        genero: vgenero,
                        tipo_ceguera: vtipoceguera,
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
                title: "Discapacitado actualizado con exito",
            });

            setTimeout(() => {
                const v_editar = document.getElementById("nav-listado");
                v_editar.setAttribute("class", "fade");

                let ocultar = document.getElementById("MostrarDiscapacitado");
                ocultar.removeAttribute("class");

                const cambiar = v_editar.parentElement;
                cambiar.insertBefore(ocultar, v_editar);
                location.reload();
            }, 3000);
        } catch (e) {
            error = e.message;
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
                `https://proyectomascotas.onrender.com/update_estado_discapacitado/${vid}`,
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
                    title: "El Discapacitado a sido desactivado de manera exitosa",
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
                `https://proyectomascotas.onrender.com/update_estado_discapacitado/${vid}`,
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
                    title: "El Discapacitado se ha activado de manera exitosa",
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
    let terminoBusqueda = "";

    $: todosFiltrados = filtrarDiscapacitados(todos, terminoBusqueda);

    function filtrarDiscapacitados(discapacitados, termino) {
        if (!termino || typeof discapacitados === "undefined")
            return discapacitados;

        const busqueda = termino.toLowerCase();
        return discapacitados.filter(
            (disc) =>
                (disc.nombre && disc.nombre.toLowerCase().includes(busqueda)) ||
                (disc.genero && disc.genero.toLowerCase().includes(busqueda)) ||
                (disc.tipo_ceguera &&
                    disc.tipo_ceguera.toLowerCase().includes(busqueda)),
        );
    }

    function buscarUsuario(termino) {
        terminoBusqueda = termino;
    }
</script>

<div id="MostrarDiscapacitado">
    <div class="mobile-users-container">
        <h2 class="mobile-users-title">
            <i class="bi bi-person-heart me-2"></i>DISCAPACITADOS
        </h2>

        <!-- Barra de búsqueda -->
        <div class="mobile-search-container">
            <i class="bi bi-search"></i>
            <input
                type="text"
                class="mobile-search-input"
                placeholder="Buscar por nombre, género o tipo de ceguera..."
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
            <!-- No mostrar nada -->
        {:else if todos.length === 0}
            <div class="no-results-message">
                <i class="bi bi-exclamation-circle"></i>
                No se encontraron resultados
            </div>
        {:else}
            <div class="mobile-table-responsive">
                <table class="mobile-table" id="myTable">
                    <thead>
                        <tr>
                            <th>Nombre</th>
                            <th>Género</th>
                            <th>Tipo de Ceguera</th>
                            <th>Estado</th>
                            <th>Acciones</th>
                        </tr>
                    </thead>
                    <tbody>
                        {#each todosFiltrados as todo}
                            <tr>
                                <td data-label="Nombre">{todo.nombre}</td>
                                <td data-label="Género">{todo.genero}</td>
                                <td data-label="Tipo de Ceguera"
                                    >{todo.tipo_ceguera}</td
                                >
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
                                            on:click={() =>
                                                editar(todo.id, todo.nombre)}
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

<!-- Modal de Edición -->
<div
    class="mobile-modal fade"
    id="nav-listado"
    role="tabpanel"
    aria-labelledby="nav-listado-tab"
>
    <div class="mobile-modal-card">
        <div class="mobile-modal-header">
            <h5><b>Editando Discapacitado</b></h5>
            <button
                class="mobile-close-btn"
                aria-label="Cerrar edición"
                on:click={() => Ocultar()}
            >
                <i class="bi bi-x-lg"></i>
            </button>
        </div>

        <div class="mobile-modal-body">
            <!-- Nombre -->
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

            <!-- Género -->
            <div class="mobile-form-group">
                <label for="genero"><b>Género:</b></label>
                <input type="text" id="genero" placeholder="Género" readonly />
            </div>

            <!-- Tipo de Ceguera -->
            <div class="mobile-form-group">
                <label for="tipo_ceguera"><b>Tipo de Ceguera:</b></label>
                <input
                    type="text"
                    id="tipo_ceguera"
                    placeholder="Tipo de Ceguera"
                    readonly
                />
            </div>

            <!-- Estado -->
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
    /* ESTILOS PRINCIPALES (iguales al ejemplo) */
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

    /* ESTILOS DEL MODAL */
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

    /* MEDIA QUERIES PARA ESCRITORIO */
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
