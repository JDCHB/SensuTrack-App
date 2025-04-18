<script>
    import { onMount, createEventDispatcher } from "svelte";
    let numero_serie = "";
    let v_nivel_bateria = 100;
    let id_ciego_vinculado = "";
    let v_estado = true;
    let mensaje = null;
    let error = null;

    //ESTOS 3 DE AQUI SON PARA LA TABLA
    let todos = {};
    let todos2 = {};

    onMount(async () => {
        const response2 = await fetch(
            "https://proyectomascotas.onrender.com/get_discapacitadosV_SIN_GPS/",
        );
        const data2 = await response2.json();
        todos2 = data2.resultado;
        console.log(todos2);
    });

    const dispatch = createEventDispatcher();

    // Función para manejar el envío del formulario
    async function RegistrarGPS() {
        try {
            dispatch("showLoader"); // 🔥 emitir evento personalizado
            const response = await fetch(
                "https://proyectomascotas.onrender.com/create_gps",
                {
                    method: "POST",
                    headers: {
                        "Content-Type": "application/json",
                    },
                    body: JSON.stringify({
                        numero_serie: numero_serie,
                        nivel_bateria: v_nivel_bateria,
                        id_ciego_vinculado: parseInt(id_ciego_vinculado),
                        estado: v_estado,
                    }),
                },
            );

            const data = await response.json();
            console.log(data);
            dispatch("hideLoader"); // 🔥 cuando termina
            if (response.ok) {
                mensaje = data.resultado; // Mensaje exitoso
                error = null; // Limpia el error si lo hubo
                numero_serie = "";
                id_ciego_vinculado = "";
                Swal.fire({
                    title: "El GPS ha sido Registrado Exitosamente!",
                    icon: "success",
                    confirmButtonText: "OK",
                });
            } else {
                swalWithBootstrapButtons.fire({
                    title: "Parece que ha ocurrido un error",
                    text: "Revise que todo este correcto porfavor :]",
                    icon: "error",
                });
                throw new Error(data.detail || "Ocurrió un error");
            }
        } catch (e) {
            error = e.message;
            dispatch("hideLoader"); // 🔥 cuando termina
            mensaje = null; // Limpia el mensaje si hubo un error
        }
    }
</script>

<div class="gps-container">
    <h2 class="gps-title">REGISTRAR GPS</h2>

    <form on:submit|preventDefault={RegistrarGPS} class="vibrant-form">
        <div class="input-group">
            <p class="input-description">
                Registre el Número de Serie del GPS:
            </p>
            <input
                id="numero_serie"
                type="text"
                bind:value={numero_serie}
                class="vibrant-input"
                placeholder="Ingrese el Serial del GPS"
                required
            />
        </div>

        <div class="input-group">
            <p class="input-description">
                Usuarios Discapacitados Registrados:
            </p>
            <select
                id="ciegos"
                class="vibrant-select"
                bind:value={id_ciego_vinculado}
                required
            >
                <option value="" disabled selected>
                    Selecciona el Discapacitado que tendrá el GPS
                </option>
                {#each todos2 as todo}
                    <option value={todo.id}>{todo.nombre}</option>
                {/each}
            </select>
        </div>

        <button class="vibrant-button">
            <i class="bi bi-check-circle-fill"></i> CONFIRMAR REGISTRO
        </button>
    </form>
</div>

<link
    href="https://cdn.jsdelivr.net/npm/bootstrap-icons@1.10.0/font/bootstrap-icons.css"
    rel="stylesheet"
/>

<style>
    /* ESTILOS VIBRANTES PARA REGISTRO DE GPS */
    .gps-container {
        max-width: 700px;
        margin: 2rem auto;
        padding: 2.5rem;
        background: white;
        border-radius: 20px;
        box-shadow: 0 10px 40px rgba(110, 72, 170, 0.2);
        background: linear-gradient(to bottom right, #ffffff, #f9f5ff);
        border: 1px solid rgba(110, 72, 170, 0.1);
    }

    .gps-title {
        text-align: center;
        margin-bottom: 2rem;
        font-size: 2rem;
        font-weight: 700;
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
        gap: 1.8rem;
    }

    .input-group {
        display: flex;
        flex-direction: column;
        gap: 0.5rem;
    }

    .input-description {
        color: #6e48aa;
        font-weight: 500;
        margin-bottom: 0.5rem;
        font-size: 0.95rem;
    }

    .vibrant-input {
        padding: 1.1rem 1.3rem;
        font-size: 1rem;
        border: 2px solid #e0e0e0;
        border-radius: 12px;
        transition: all 0.3s ease;
        background: #f8f9fa;
        box-shadow: 0 2px 10px rgba(0, 0, 0, 0.05);
    }

    .vibrant-input:focus {
        border-color: #9d50bb;
        box-shadow: 0 0 0 3px rgba(157, 80, 187, 0.3);
        outline: none;
        background: white;
        transform: translateY(-2px);
    }

    .vibrant-input::placeholder {
        color: #a0a0a0;
        font-weight: 400;
    }

    .vibrant-select {
        padding: 1.1rem 1.3rem;
        font-size: 1rem;
        border: 2px solid #e0e0e0;
        border-radius: 12px;
        background: #f8f9fa;
        appearance: none;
        background-image: url("data:image/svg+xml;charset=UTF-8,%3csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 24 24' fill='%236e48aa'%3e%3cpath d='M7 10l5 5 5-5z'/%3e%3c/svg%3e");
        background-repeat: no-repeat;
        background-position: right 1.3rem center;
        background-size: 1.2rem;
        transition: all 0.3s ease;
        box-shadow: 0 2px 10px rgba(0, 0, 0, 0.05);
    }

    .vibrant-select:focus {
        border-color: #9d50bb;
        box-shadow: 0 0 0 3px rgba(157, 80, 187, 0.3);
        outline: none;
        background-color: white;
        transform: translateY(-2px);
    }

    .vibrant-button {
        background: linear-gradient(135deg, #6e48aa 0%, #9d50bb 100%);
        color: white;
        border: none;
        padding: 1.2rem;
        font-size: 1.1rem;
        font-weight: 700;
        border-radius: 12px;
        cursor: pointer;
        transition: all 0.3s ease;
        text-transform: uppercase;
        letter-spacing: 1px;
        margin-top: 1rem;
        box-shadow: 0 6px 20px rgba(110, 72, 170, 0.3);
        display: flex;
        align-items: center;
        justify-content: center;
        gap: 0.8rem;
    }

    .vibrant-button:hover {
        transform: translateY(-4px);
        box-shadow: 0 8px 25px rgba(110, 72, 170, 0.4);
        background: linear-gradient(135deg, #7d52b5 0%, #a85fc7 100%);
    }

    .vibrant-button:active {
        transform: translateY(-1px);
    }

    /* EFECTO DE ONDAS AL HACER CLIC */
    .vibrant-button {
        position: relative;
        overflow: hidden;
    }

    .vibrant-button:after {
        content: "";
        position: absolute;
        top: 50%;
        left: 50%;
        width: 5px;
        height: 5px;
        background: rgba(255, 255, 255, 0.5);
        opacity: 0;
        border-radius: 100%;
        transform: scale(1, 1) translate(-50%);
        transform-origin: 50% 50%;
    }

    .vibrant-button:focus:not(:active)::after {
        animation: ripple 0.6s ease-out;
    }

    @keyframes ripple {
        0% {
            transform: scale(0, 0);
            opacity: 0.5;
        }
        100% {
            transform: scale(20, 20);
            opacity: 0;
        }
    }

    /* RESPONSIVE */
    @media (max-width: 768px) {
        .gps-container {
            padding: 1.8rem;
            margin: 1.5rem;
        }

        .gps-title {
            font-size: 1.6rem;
        }

        .vibrant-form {
            gap: 1.5rem;
        }

        .vibrant-input,
        .vibrant-select {
            padding: 1rem;
        }

        .vibrant-button {
            padding: 1rem;
            font-size: 1rem;
        }
    }

    @media (max-width: 480px) {
        .gps-container {
            padding: 1.5rem;
        }

        .gps-title {
            font-size: 1.4rem;
        }
    }
</style>
