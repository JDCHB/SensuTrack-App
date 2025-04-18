<script>
    const serviceID = "service_j9bousa";
    const templateID = "template_zszjdla";
    const apikey = "dFD1OdFitzwQblEX0";
    let cn1 = "";
    let vl_correo = "";
    let vr = 0;

    // Referencias a los contenedores de los loader
    let registerLoader;

    // Función para mostrar el loader
    function showLoader(loader) {
        if (loader) {
            loader.style.display = "flex";
        }
    }

    // Función para ocultar el loader
    function hideLoader(loader) {
        if (loader) {
            loader.style.display = "none";
        }
    }

    //Can change 7 to 2 for longer results.
    function codigo() {
        vr = (Math.random() + 1).toString(36).substring(4);
        return vr;
    }

    async function buscar_correo() {
        vl_correo = document.getElementById("correo").value; //

        try {
            showLoader(registerLoader); // Mostrar loader al comenzar el registro
            const response = await fetch(
                "https://proyectomascotas.onrender.com/Validar_Correo",
                {
                    method: "POST",
                    headers: {
                        "Content-Type": "application/json",
                    },
                    body: JSON.stringify({
                        email: vl_correo, // Enviar el valor como JSON
                    }),
                },
            );

            const data = await response.json();
            console.log(data);
            hideLoader(registerLoader); // Ocultar loader al terminar el registro
            if (data.email) {
                codigo(vr);

                emailjs.init(apikey);
                emailjs
                    .send(serviceID, templateID, {
                        r: vr,
                        email: vl_correo,
                    })
                    .then((result) => {})
                    .catch((error) => {});

                mostrarModalValidacion();
            } else {
                Swal.fire({
                    position: "top",
                    icon: "error",
                    title: "Correo no encontrado",
                });
            }
        } catch (e) {
            console.log(e);
        }
    }

    async function cambiar_contrasena() {
        try {
            showLoader(registerLoader); // Mostrar loader al comenzar el registro
            const response = await fetch(
                "https://proyectomascotas.onrender.com/update_contraseña",
                {
                    method: "PUT",
                    headers: {
                        "Content-Type": "application/json",
                    },
                    body: JSON.stringify({
                        email: vl_correo,
                        password: cn1,
                    }),
                },
            );
            const data = await response.json();
            console.log("Respuesta del servidor:", data);
            hideLoader(registerLoader); // Ocultar loader al terminar el registro
            if (data.mensaje == "Contraseña actualizada exitosamente") {
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
                    background: "green",
                    title: "Contraseñass cambiada exitosamente",
                });

                setTimeout(() => {
                    window.location.href = "/";
                }, 3500);
            } else {
                Swal.fire({
                    title: "Error",
                    text: "Hubo un problema al cambiar la contraseña.",
                    icon: "error",
                });
            }
        } catch (e) {
            console.log(e);
        }
    }

    function mostrarModalValidacion() {
        Swal.fire({
            title: "Validacion",
            html: `
                        <div class="card-header">Ingrese la clave enviada al correo<div/>
                        <input class="mt-2 form-control" id="ingaturrona"></input>
                    `,
            icon: "warning",
            showCancelButton: true,
            allowOutsideClick: false,
            confirmButtonColor: "#3085d6",
            cancelButtonColor: "#d33",
            confirmButtonText: "Validar",
        }).then((result) => {
            const f = document.getElementById("ingaturrona").value;
            if (result.isConfirmed) {
                if (f == vr) {
                    Swal.fire({
                        title: "Cambio de clave",
                        html: `
                                    <div class="card-header">Ingrese su nueva clave<div/>
                                    <input class="mt-2 form-control" id="clave_nueva_1"></input>
                                `,
                        icon: "warning",
                        allowOutsideClick: false,
                        confirmButtonColor: "#3085d6",
                        confirmButtonText: "Validar",
                    }).then((result) => {
                        cn1 = document.getElementById("clave_nueva_1").value;
                        if (result.isConfirmed) {
                            cambiar_contrasena(cn1);
                        }
                    });
                } else {
                    Swal.fire({
                        title: "Error",
                        text: "El codigo es incorrecto... Por favor, intentalo de nuevo.",
                        icon: "error",
                    }).then(() => {
                        mostrarModalValidacion();
                    });
                }
            } else if (result.isDismissed) {
            }
        });
    }
</script>

<div class="password-container">
    <div class="password-card">
        <div class="password-header">
            <h2 class="password-title">Cambio de Contraseña</h2>
            <p class="password-subtitle">
                Por favor, ingrese su correo electrónico registrado en el
                sistema
            </p>
        </div>

        <div class="password-form">
            <div class="input-group">
                <label for="correo" class="vibrant-label"
                    >Correo Electrónico</label
                >
                <input
                    type="email"
                    class="vibrant-input"
                    placeholder="ejemplo@correo.com"
                    id="correo"
                    bind:value={vl_correo}
                    required
                />
            </div>

            <button on:click={buscar_correo} class="vibrant-button">
                <i class="bi bi-send-fill"></i> ENVIAR CÓDIGO
            </button>
        </div>
    </div>

    <!-- Loader con estilo vibrante -->
    <div class="loader-container" bind:this={registerLoader}>
        <div class="vibrant-loader">
            <span>S</span>
            <span>e</span>
            <span>n</span>
            <span>s</span>
            <span>u</span>
            <span>T</span>
            <span>r</span>
            <span>a</span>
            <span>c</span>
            <span>k</span>
        </div>
    </div>
</div>

<link
    href="https://cdn.jsdelivr.net/npm/bootstrap-icons@1.10.0/font/bootstrap-icons.css"
    rel="stylesheet"
/>

<style>
    /* ESTILOS VIBRANTES PARA CAMBIO DE CONTRASEÑA */
    .password-container {
        max-width: 600px;
        margin: 2rem auto;
        position: relative;
    }

    .password-card {
        background: white;
        border-radius: 20px;
        padding: 2.5rem;
        box-shadow: 0 15px 40px rgba(110, 72, 170, 0.2);
        background: linear-gradient(to bottom right, #ffffff, #f9f5ff);
        border: 1px solid rgba(110, 72, 170, 0.1);
    }

    .password-header {
        text-align: center;
        margin-bottom: 2rem;
    }

    .password-title {
        font-size: 2.2rem;
        font-weight: 700;
        margin-bottom: 0.5rem;
        background: linear-gradient(90deg, #6e48aa, #9d50bb);
        -webkit-background-clip: text;
        background-clip: text;
        color: transparent;
    }

    .password-subtitle {
        color: #6e48aa;
        font-size: 1.1rem;
        opacity: 0.9;
    }

    .password-form {
        display: flex;
        flex-direction: column;
        gap: 1.8rem;
    }

    .input-group {
        display: flex;
        flex-direction: column;
        gap: 0.8rem;
    }

    .vibrant-label {
        font-weight: 600;
        color: #5a5a5a;
        font-size: 1.1rem;
        text-align: left;
    }

    .vibrant-input {
        padding: 1.2rem 1.5rem;
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

    /* LOADER VIBRANTE */
    .loader-container {
        position: fixed;
        top: 0;
        left: 0;
        right: 0;
        bottom: 0;
        background: rgba(248, 249, 250, 0.95);
        display: none;
        justify-content: center;
        align-items: center;
        z-index: 100;
        backdrop-filter: blur(3px);
    }

    .vibrant-loader {
        display: flex;
        font-size: 2.5rem;
        font-weight: bold;
        letter-spacing: 3px;
        background: linear-gradient(90deg, #6e48aa, #9d50bb, #4776e6, #8e54e9);
        -webkit-background-clip: text;
        background-clip: text;
        color: transparent;
        background-size: 300% 300%;
        animation: gradient 4s ease infinite;
    }

    .vibrant-loader span {
        animation: bounce 1.5s infinite ease-in-out;
        animation-delay: calc(0.1s * var(--i));
    }

    @keyframes gradient {
        0% {
            background-position: 0% 50%;
        }
        50% {
            background-position: 100% 50%;
        }
        100% {
            background-position: 0% 50%;
        }
    }

    @keyframes bounce {
        0%,
        100% {
            transform: translateY(0);
            opacity: 0.8;
        }
        50% {
            transform: translateY(-15px);
            opacity: 1;
        }
    }

    /* RESPONSIVE */
    @media (max-width: 768px) {
        .password-card {
            padding: 2rem 1.5rem;
            margin: 1rem;
        }

        .password-title {
            font-size: 1.8rem;
        }

        .vibrant-input {
            padding: 1rem;
        }

        .vibrant-button {
            padding: 1rem;
            font-size: 1rem;
        }
    }

    @media (max-width: 480px) {
        .password-title {
            font-size: 1.6rem;
        }

        .password-subtitle {
            font-size: 1rem;
        }

        .vibrant-loader {
            font-size: 2rem;
        }
    }
</style>
