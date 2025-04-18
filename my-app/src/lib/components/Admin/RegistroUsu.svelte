<script>
    import { onMount, createEventDispatcher } from "svelte";

    let v_nombre = "";
    let v_apellido = "";
    let v_password = "";
    let Confirmar_Contraseña = "";
    let v_documento = "";
    let v_telefono = "";
    let v_email = "";
    let v_rol = "";
    let roles = [];
    let v_estado = true;
    let error = null;
    let registerLoader;

    onMount(async () => {
        const response = await fetch(
            "https://proyectomascotas.onrender.com/get_roles/",
        );
        const data = await response.json();
        roles = data.resultado;
        console.log(roles);
    });

    const dispatch = createEventDispatcher();

    async function Register() {
        Confirmar_Contraseña = document.getElementById(
            "Confirmar_Contraseña",
        ).value;

        if (Confirmar_Contraseña === v_password) {
            try {
                const result = await Swal.fire({
                    title: "¿Estás seguro?",
                    text: "¡Desea registrar un nuevo Usuario a SensuTrack!?",
                    icon: "warning",
                    showCancelButton: true,
                    confirmButtonColor: "#3085d6",
                    cancelButtonColor: "#d33",
                    confirmButtonText: "Sí, registrar!",
                    customClass: {
                        popup: "swal-popup",
                        title: "custom-title",
                    },
                });

                // Convertir el rol seleccionado a número entero
                const id_rol = parseInt(v_rol, 10);

                // Validar que el rol es un número válido
                if (isNaN(id_rol)) {
                    alert("Por favor, seleccione un rol válido.");
                    dispatch("hideLoader"); // 🔥 cuando termina
                    return;
                }

                if (result.isConfirmed) {
                    dispatch("showLoader"); // 🔥 emitir evento personalizado

                    const response = await fetch(
                        "https://proyectomascotas.onrender.com/create_user",
                        {
                            method: "POST",
                            headers: {
                                "Content-Type": "application/json",
                            },
                            body: JSON.stringify({
                                email: v_email,
                                password: v_password,
                                nombre: v_nombre,
                                apellido: v_apellido,
                                documento: v_documento,
                                telefono: v_telefono,
                                id_rol: v_rol,
                                estado: v_estado,
                            }),
                        },
                    );

                    const data = await response.json();
                    console.log("Respuesta del servidor:", data);
                    dispatch("hideLoader"); // 🔥 cuando termina

                    if (data.resultado === "Usuario creado") {
                        Swal.fire({
                            title: "¡Registrado!,¡Bienvenid@ " + v_nombre + "!",
                            icon: "success",
                            customClass: {
                                popup: "swal-popup",
                                title: "custom-title",
                            },
                        });
                    } else if (data.resultado === "El usuario ya existe") {
                        Swal.fire({
                            icon: "error",
                            title: "Error",
                            text: "Lo sentimos, el usuario ya existe.",
                        });
                    }
                } else {
                    Swal.fire({
                        title: "REGISTRO CANCELADO",
                        icon: "error",
                        customClass: {
                            popup: "swal-popup", // Clase para personalizar el popup de la alerta
                            title: "custom-title", // Clase personalizada para el título
                        },
                    });
                }
            } catch (e) {
                error = e.message;
                dispatch("hideLoader"); // 🔥 cuando termina
                alert("Error en la solicitud: " + error);
            }
        } else {
            Swal.fire({
                title: "Parece que ha ocurrido un error",
                text: "No son iguales las contraseñas, porfavor intente de nuevo :]",
                icon: "error",
                customClass: {
                    popup: "swal-popup", // Clase para personalizar el popup de la alerta
                    title: "custom-title", // Clase personalizada para el título
                },
            });
        }
    }
</script>

<div class="form-wrapper">
    <div class="form-title">REGISTRO DE USUARIOS</div>
    <form on:submit|preventDefault={Register} class="vibrant-form">
        <input
            class="vibrant-input"
            bind:value={v_nombre}
            placeholder="Nombre"
            type="text"
            autocomplete="off"
            required
        />
        <input
            class="vibrant-input"
            bind:value={v_apellido}
            placeholder="Apellido"
            type="text"
            autocomplete="off"
            required
        />
        <input
            class="vibrant-input"
            bind:value={v_documento}
            placeholder="Documento"
            type="text"
            autocomplete="off"
            required
        />
        <input
            class="vibrant-input"
            bind:value={v_telefono}
            placeholder="Teléfono"
            type="text"
            autocomplete="off"
            required
        />
        <input
            class="vibrant-input"
            bind:value={v_email}
            placeholder="Correo Electrónico"
            type="email"
            autocomplete="off"
            required
        />
        <input
            class="vibrant-input"
            bind:value={v_password}
            placeholder="Contraseña"
            type="password"
            autocomplete="off"
            required
        />
        <input
            class="vibrant-input"
            id="Confirmar_Contraseña"
            placeholder="Confirmar Contraseña"
            type="password"
            autocomplete="off"
            required
        />

        <select id="roles" class="vibrant-select" bind:value={v_rol}>
            <option value="" disabled selected>Seleccione el Rol:</option>
            {#each roles as rol}
                <option value={rol.id}>{rol.nombre}</option>
            {/each}
        </select>

        <button class="vibrant-button">REGISTRAR USUARIO</button>
    </form>
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

    .vibrant-select {
        padding: 1rem 1.2rem;
        font-size: 1rem;
        border: 2px solid #e0e0e0;
        border-radius: 10px;
        background: #f8f9fa;
        appearance: none;
        background-image: url("data:image/svg+xml;charset=UTF-8,%3csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 24 24' fill='%236e48aa'%3e%3cpath d='M7 10l5 5 5-5z'/%3e%3c/svg%3e");
        background-repeat: no-repeat;
        background-position: right 1rem center;
        background-size: 1.2rem;
        transition: all 0.3s ease;
    }

    .vibrant-select:focus {
        border-color: #9d50bb;
        box-shadow: 0 0 0 3px rgba(157, 80, 187, 0.2);
        outline: none;
        background-color: white;
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

        .vibrant-input,
        .vibrant-select {
            padding: 0.9rem;
        }
    }
</style>
