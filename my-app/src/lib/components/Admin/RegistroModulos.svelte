<script>
    import { onMount, createEventDispatcher } from "svelte";

    let v_nombre = "";
    let v_descripcion = "";
    let v_ubicacion = "";
    let v_estilo = "";
    let v_estado = true;

    //ESTOS 3 DE AQUI SON PARA LA TABLA
    let todos = {};
    let loading = true;
    let error = null;

    const dispatch = createEventDispatcher();

    async function RegisterModulo() {
        try {
            // Muestra el cuadro de confirmación antes de proceder con el registro
            const result = await Swal.fire({
                title: "¿Estás seguro?",
                text: "¡Desea registrar el siguiente Modulo!?: " + v_nombre,
                icon: "warning",
                showCancelButton: true,
                confirmButtonColor: "#3085d6",
                cancelButtonColor: "#d33",
                confirmButtonText: "Sí, registrar!",
                customClass: {
                    popup: "swal-popup", // Clase para personalizar el popup de la alerta
                    title: "custom-title", // Clase personalizada para el título
                },
            });
            // Si el usuario confirma, continuamos con el registro
            if (result.isConfirmed) {
                dispatch("showLoader"); // 🔥 emitir evento personalizado
                const response = await fetch(
                    "https://proyectomascotas.onrender.com/create_modulo",
                    {
                        method: "POST",
                        headers: {
                            "Content-Type": "application/json",
                        },
                        body: JSON.stringify({
                            nombre: v_nombre,
                            descripcion: v_descripcion,
                            ubicacion: v_ubicacion,
                            estado: v_estado,
                            estilo: v_estilo,
                        }),
                    },
                );

                const data = await response.json();
                console.log(data);

                dispatch("hideLoader"); // 🔥 cuando termina

                if (response.ok) {
                    Swal.fire({
                        title:
                            "¡Se ha registrado el Modulo llamado " +
                            v_nombre +
                            " con exito!",
                        icon: "success",
                        customClass: {
                            popup: "swal-popup", // Clase para personalizar el popup de la alerta
                            title: "custom-title", // Clase personalizada para el título
                        },
                    });
                    setTimeout(() => {
                        location.reload();
                    }, 3500);
                } else {
                    alert("Error en el registro");
                }
            } else {
                console.log("Registro Cancelado");
            }
        } catch (e) {
            error = e.message;
            dispatch("hideLoader"); // 🔥 cuando termina
            alert("Error en la solicitud: " + error);
        }
    }

    //DE AQUI PARA ABAJO SON SCRIPTS DE LAS TABLAS

    onMount(async () => {
        try {
            console.log("2");
            const response = await fetch(
                "https://proyectomascotas.onrender.com/get_modulos",
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
        const v_editar = document.getElementById("nav-listado");
        v_editar.setAttribute("class", "fade");

        let mostrar = document.getElementById("MostrarModulos");
        mostrar.removeAttribute("class");

        location.reload();
    }

    var vid = 1;
    async function editar(id, a) {
        console.log("Editando el modulo " + a);
        const v_editar = document.getElementById("nav-listado");
        v_editar.removeAttribute("class");
        console.log(v_editar);
        vid = id;
        console.log(Number.isInteger(vid));

        console.log(vid);
        let ocultar = document.getElementById("MostrarModulos");
        ocultar.setAttribute("class", "fade");
        console.log(ocultar);

        const cambiar = ocultar.parentElement;
        console.log(cambiar);

        cambiar.insertBefore(v_editar, ocultar);
        console.log("NO Entra al try de buscar");

        try {
            console.log("Entra al try de buscar");

            const response = await fetch(
                `https://proyectomascotas.onrender.com/get_modulo/${vid}`,
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
            console.log("Buscando el Modulo seleccionado");
            todos = data.nombre;
            console.log(todos);
            document.getElementById("nombre").value = data.nombre;
            document.getElementById("descripcion").value = data.descripcion;
            document.getElementById("ubicacion").value = data.ubicacion;
            document.getElementById("estilo").value = data.estilo;
            console.log("verificando el estado: " + data.estado);
            const estado_v = data.estado ? "1" : "0"; //condicion ? valorSiVerdadero : valorSiFalso
            document.getElementById("estado").value = estado_v;

            const v_edit_nombre = document.getElementById("nombre");
            v_edit_nombre.removeAttribute("readonly");
            v_edit_nombre.focus();

            const v_edit_descripcion = document.getElementById("descripcion");
            v_edit_descripcion.removeAttribute("readonly");

            const v_edit_ubicacion = document.getElementById("ubicacion");
            v_edit_ubicacion.removeAttribute("readonly");

            const v_edit_estilo = document.getElementById("estilo");
            v_edit_estilo.removeAttribute("readonly");

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
        let v_nombre = document.getElementById("nombre").value;
        let v_descripcion = document.getElementById("descripcion").value;
        let v_ubicacion = document.getElementById("ubicacion").value;
        let v_estilo = document.getElementById("estilo").value;
        let vestado = document.getElementById("estado").value;

        console.log("ID DE ESTADO ENVIADO A LA BASE DE DATOS ES " + vestado);

        try {
            console.log("Entra al try de actualzar");

            const response = await fetch(
                `https://proyectomascotas.onrender.com/update_modulo/${vid}`, // Incluye el user_id en la URL
                {
                    method: "PUT",
                    headers: {
                        "Content-Type": "application/json",
                    },
                    body: JSON.stringify({
                        nombre: v_nombre,
                        descripcion: v_descripcion,
                        ubicacion: v_ubicacion,
                        estilo: v_estilo,
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
                title: "Modulo actualizado exitosamente",
            });

            setTimeout(() => {
                const v_editar = document.getElementById("nav-listado");
                v_editar.setAttribute("class", "fade");

                let ocultar = document.getElementById("MostrarModulos");
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

        // Convertir el valor a booleano
        let estadoBooleano = vestado !== 0;
        //console.log("Correo" + email);
        try {
            const response = await fetch(
                `https://proyectomascotas.onrender.com/update_estado_modulo/${vid}`,
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
                    title: "El Modulo a sido desactivado de manera exitosa",
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
                `https://proyectomascotas.onrender.com/update_estado_modulo/${vid}`,
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
                    title: "El Modulo se ha activado de manera exitosa",
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
</script>

<div class="form-wrapper">
    <div class="form-title">REGISTRO DE MODULOS</div>
    <form on:submit|preventDefault={RegisterModulo} class="vibrant-form">
        <input
            class="vibrant-input"
            bind:value={v_nombre}
            placeholder="Nombre del Modulo"
            type="text"
            required
        />
        <input
            class="vibrant-input"
            bind:value={v_descripcion}
            placeholder="Descripcion del Modulo"
            type="text"
            required
        />
        <input
            class="vibrant-input"
            bind:value={v_ubicacion}
            placeholder="Ubicacion del Modulo"
            type="text"
            required
        />
        <input
            class="vibrant-input"
            bind:value={v_estilo}
            placeholder="Ingrese el icono que tendra el Modulo (Boostrap)"
            type="text"
            required
        />
        <button class="vibrant-button">Confirmar</button>
    </form>
</div>

<div id="MostrarModulos">
    <div class="modulos-container">
        <h2 class="roles-title">
            <i class="bi bi-diagram-3"></i> Lista de Modulos
        </h2>
        {#if loading}
            <div class="mobile-loading">
                <div class="spinner-border text-primary" role="status">
                    <span class="visually-hidden">Loading...</span>
                </div>
                <div>Cargando datos...</div>
            </div>
        {:else if error}
            <div class="alert alert-danger text-center">{error}</div>
        {:else}
            <div class="mobile-table-responsive">
                <table class="mobile-table" id="myTable">
                    <thead>
                        <tr>
                            <th>Nombre</th>
                            <th>Descripcion</th>
                            <th>Estado</th>
                            <th>Opciones</th>
                        </tr>
                    </thead>

                    <tbody>
                        {#each todos as todo}
                            <tr>
                                <td data-label="Nombre">{todo.nombre}</td>
                                <td data-label="Descripción"
                                    >{todo.descripcion}</td
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
                                <td data-label="Opciones" class="text-center">
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
            <h5><b>Editando Modulo</b></h5>
            <button
                class="mobile-close-btn"
                aria-label="Cerrar edición"
                on:click={() => Ocultar()}
            >
                <i class="bi bi-x-lg"></i>
            </button>
        </div>
        <div class="mobile-modal-body">
            <div class="mobile-form-group">
                <label for="nombre"><b>Nombre:</b></label>
                <input
                    type="text"
                    id="nombre"
                    placeholder="Nombre del Rol"
                    maxlength="100"
                    readonly
                />
            </div>

            <div class="mobile-form-group">
                <label for="descripcion"><b>Descripcion:</b></label>
                <input
                    type="text"
                    id="descripcion"
                    placeholder="Descripcion del Modulo"
                    maxlength="100"
                    readonly
                />
            </div>

            <div class="mobile-form-group">
                <label for="ubicacion"><b>Ubicación:</b></label>
                <input
                    type="text"
                    id="ubicacion"
                    placeholder="Ubicación del Modulo"
                    maxlength="100"
                    readonly
                />
            </div>

            <div class="mobile-form-group">
                <label for="estilo"><b>Estilo:</b></label>
                <input
                    type="text"
                    id="estilo"
                    placeholder="Ingrese el icono que tendra el Modulo (Boostrap)"
                    maxlength="100"
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

<style>
    /* FORMULARIO CON ESTILO VIBRANTE */
    .form-wrapper {
        max-width: 500px;
        margin: 2rem auto;
        padding: 2rem;
        background: white;
        border-radius: 16px;
        box-shadow: 0 8px 30px rgba(110, 72, 170, 0.2);
    }

    .form-title {
        font-size: 1.8rem;
        font-weight: 700;
        text-align: center;
        margin-bottom: 2rem;
        background: linear-gradient(90deg, #6e48aa, #9d50bb);
        -webkit-background-clip: text;
        background-clip: text;
        color: transparent;
        text-transform: uppercase;
        letter-spacing: 1px;
    }

    .vibrant-form {
        display: flex;
        flex-direction: column;
        gap: 1.2rem;
    }

    .vibrant-input {
        padding: 1rem 1.2rem;
        font-size: 1rem;
        border: 2px solid #e0e0e0;
        border-radius: 10px;
        transition: all 0.3s ease;
        background: #f8f9fa;
    }

    .vibrant-input:focus {
        border-color: #9d50bb;
        box-shadow: 0 0 0 3px rgba(157, 80, 187, 0.2);
        outline: none;
        background: white;
        transform: translateY(-2px);
    }

    .vibrant-input::placeholder {
        color: #a0a0a0;
    }

    .vibrant-button {
        background: linear-gradient(135deg, #6e48aa 0%, #9d50bb 100%);
        color: white;
        border: none;
        padding: 1.2rem;
        font-size: 1.1rem;
        font-weight: 600;
        border-radius: 10px;
        cursor: pointer;
        transition: all 0.3s ease;
        text-transform: uppercase;
        letter-spacing: 1px;
        margin-top: 1rem;
        box-shadow: 0 4px 15px rgba(110, 72, 170, 0.3);
    }

    .vibrant-button:hover {
        transform: translateY(-3px);
        box-shadow: 0 6px 20px rgba(110, 72, 170, 0.4);
    }

    .vibrant-button:active {
        transform: translateY(-1px);
    }

    /* Efecto para campos inválidos */
    .vibrant-input:invalid:not(:placeholder-shown) {
        border-color: #ff4757;
    }

    /* Responsive */
    @media (max-width: 600px) {
        .form-wrapper {
            padding: 1.5rem;
            margin: 1rem;
        }

        .form-title {
            font-size: 1.5rem;
        }

        .vibrant-input {
            padding: 0.9rem;
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
    /* TABLA Y MODAL */

    /* ESTILOS GENERALES */
    .modulos-container {
        width: 100%;
        padding: 1.5rem;
        background: #fff;
        border-radius: 12px;
        box-shadow: 0 4px 20px rgba(0, 0, 0, 0.08);
        margin: 1rem auto;
        max-width: 1200px;
    }

    .roles-title {
        color: #6e48aa;
        text-align: center;
        font-size: 1.8rem;
        margin-bottom: 1.5rem;
        display: flex;
        align-items: center;
        justify-content: center;
        gap: 0.5rem;
        padding-bottom: 1rem;
        border-bottom: 2px solid #d1c4e9;
    }

    /* TABLA */
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

    /* BADGES */
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

    /* BOTONES DE ACCIÓN */
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

    /* MODAL */
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
        margin-bottom: 1.5rem;
    }

    .mobile-form-group label {
        display: block;
        margin-bottom: 0.75rem;
        font-weight: 500;
    }

    .mobile-form-group input,
    .mobile-form-group select {
        width: 100%;
        padding: 0.75rem;
        border: 2px solid #ddd;
        border-radius: 8px;
    }

    /* BOTÓN ACTUALIZAR */
    .mobile-update-btn {
        background: linear-gradient(135deg, #6e48aa 0%, #9d50bb 100%);
        color: white;
        border: none;
        padding: 0.75rem 1.5rem;
        border-radius: 8px;
        font-weight: bold;
        width: 100%;
    }

    /* RESPONSIVE */
    @media (min-width: 992px) {
        .mobile-table thead {
            display: table-header-group;
            background: #f3e5f5;
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

        .mobile-modal-card {
            width: 60%;
        }
    }
</style>
