<script>
    import { goto } from "$app/navigation";
    let v_usuario = "";
    let v_password = "";
    let error = null;
    let showPassword = false;

    // Referencias a los contenedores de los loader
    let loginLoader;

    // Función para mostrar el loader
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

    async function Login() {
        const v_usuario = document.getElementById("correo").value;
        const v_password = document.getElementById("contraseña").value;

        try {
            showLoader(loginLoader); // Mostrar loader
            const response = await fetch(
                "https://proyectomascotas.onrender.com/login_generate_token",
                {
                    method: "POST",
                    headers: {
                        "Content-Type": "application/json",
                    },
                    body: JSON.stringify({
                        email: v_usuario,
                        password: v_password,
                    }),
                },
            );

            const data = await response.json();
            hideLoader(loginLoader); // Ocultar loader

            if (response.ok) {
                // Guardar token y datos en localStorage
                const { access_token, user_data } = data; // Extraer token y datos del usuario
                localStorage.setItem("access_token", access_token);
                localStorage.setItem("user_data", JSON.stringify(user_data));
                console.log(user_data);

                if (user_data.id_rol == 3) {
                    let todos = user_data;
                    let v_nombre = user_data.nombre;
                    Swal.fire({
                        title: "Inicio de Sesión Exitoso",
                        text: "¡Bienvenido al Sistema de Super Administrador!",
                        icon: "success",
                        confirmButtonText: "OK",
                    }).then(() => {
                        goto("Super_Administrador");
                    });
                } else if (user_data.id_rol == 2) {
                    Swal.fire({
                        icon: "warning",
                        title: "Lo sentimos",
                        text: "Ahora mismo no esta disponible este Rol (Usuario) ಥ_ಥ",
                    }).then(() => {
                        localStorage.clear();
                        window.location.href = "/";
                    });
                } else if (user_data.id_rol == 1) {
                    Swal.fire({
                        icon: "warning",
                        title: "Lo sentimos",
                        text: "Ahora mismo no esta disponible este Rol (Administrador) ಥ_ಥ",
                    }).then(() => {
                        localStorage.clear();
                        window.location.href = "/";
                    });
                }
            } else {
                // Si la respuesta es 403, mostrar un mensaje diferente
                if (response.status === 403) {
                    Swal.fire({
                        icon: "warning",
                        title: "Cuenta Desactivada",
                        text:
                            data.detail ||
                            "Tu cuenta ha sido desactivada. Contacta al soporte.",
                    });
                } else {
                    Swal.fire({
                        icon: "error",
                        title: "Oops...",
                        text:
                            data.detail || "Usuario o Contraseña Incorrectos!",
                    });
                }
            }
        } catch (e) {
            console.error("Error en la solicitud:", e.message);
            hideLoader(loginLoader);
            Swal.fire({
                icon: "error",
                title: "Error",
                text: "Hubo un problema con el inicio de sesión. Intenta nuevamente.",
            });
        }
    }
</script>

<div class="login-screen">
    <div class="overlay"></div>

    <div class="login-content">
        <!-- Logo o ícono (puedes reemplazarlo con un SVG si quieres) -->
        <div class="logo"><img src="/logo.jpg" alt="" /></div>

        <!-- Formulario de inicio de sesión -->
        <form on:submit={Login}>
            <div class="input-group">
                <span class="icon">👤</span>
                <input
                    type="text"
                    id="correo"
                    bind:value={v_usuario}
                    placeholder="Correo"
                    required
                />
            </div>

            <div class="input-group">
                <span class="icon">🔒</span>
                <input
                    type="password"
                    id="contraseña"
                    bind:value={v_password}
                    placeholder="Contraseña"
                    required
                />
            </div>

            <!-- Botón de inicio de sesión -->
            <button class="login-button" type="submit">Iniciar Sesión</button>

            <!-- Loader del login -->
            <div class="loader-container" bind:this={loginLoader}>
                <div class="loader-text">
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
        </form>

        <!-- Links -->
        <div class="links">
            <div class="link-item">
                <a href="/Cambio_Clave">Olvidaste la contraseña?</a>
            </div>
            <!-- <div class="link-item">
                <a href="/">No tienes cuenta? Regístrate!!</a>
            </div> -->
        </div>
    </div>
</div>

<style>
    /* Contenedor para el loader */
    .loader-container {
        position: fixed; /* Usamos fixed para que se quede fijo en toda la pantalla */
        top: 0;
        left: 0;
        right: 0;
        bottom: 0;
        background: rgba(0, 0, 0, 0.6); /* Fondo oscuro más intenso */
        display: none;
        justify-content: center;
        align-items: center;
        z-index: 100;
        backdrop-filter: blur(5px); /* Añadir desenfoque al fondo */
    }

    /* Loader Text */
    .loader-text {
        display: flex;
        font-size: 48px;
        font-weight: bold;
        letter-spacing: 5px;
        color: white; /* Color blanco para mejorar la visibilidad */
        text-shadow: 0 0 10px rgba(0, 0, 0, 0.5); /* Sombra para mejorar contraste */
        animation: fadeIn 1.5s infinite alternate;
    }

    .loader-text span {
        animation: bounce 1.5s infinite ease-in-out;
        animation-delay: calc(0.1s * var(--i));
    }

    .loader-text span:nth-child(1) {
        --i: 1;
    }
    .loader-text span:nth-child(2) {
        --i: 2;
    }
    .loader-text span:nth-child(3) {
        --i: 3;
    }
    .loader-text span:nth-child(4) {
        --i: 4;
    }
    .loader-text span:nth-child(5) {
        --i: 5;
    }
    .loader-text span:nth-child(6) {
        --i: 6;
    }
    .loader-text span:nth-child(7) {
        --i: 7;
    }
    .loader-text span:nth-child(8) {
        --i: 8;
    }
    .loader-text span:nth-child(9) {
        --i: 9;
    }
    .loader-text span:nth-child(10) {
        --i: 10;
    }

    @keyframes bounce {
        0%,
        100% {
            transform: translateY(0);
            opacity: 0.7;
        }
        50% {
            transform: translateY(-15px);
            opacity: 1;
        }
    }

    @keyframes fadeIn {
        0% {
            opacity: 0.7;
        }
        100% {
            opacity: 1;
        }
    }

    /* FORMULARIO */

    .login-screen {
        position: absolute;
        top: 0;
        left: 0;
        right: 0;
        bottom: 0;
        background-image: url("/login.jpg"); /* Cambia por tu imagen */
        background-size: cover;
        background-position: center;
        display: flex;
        justify-content: center;
        align-items: center;
    }

    .overlay {
        position: absolute;
        inset: 0;
        background: rgba(0, 0, 0, 0.4);
        backdrop-filter: blur(1px);
        z-index: 1;
    }

    .login-content {
        position: relative;
        z-index: 2;
        width: 100%;
        max-width: 320px;
        text-align: center;
        color: white;
        padding: 20px;
    }

    .logo {
        display: flex;
        justify-content: center; /* Centra la imagen horizontalmente */
        align-items: center; /* Centra la imagen verticalmente */
        margin-bottom: 40px;
    }

    .logo img {
        width: 100px; /* Ajusta el tamaño del logo */
        height: 100px; /* Asegura que la imagen sea cuadrada */
        border-radius: 50%; /* Hace la imagen en forma de círculo */
        object-fit: cover; /* Asegura que la imagen se recorte adecuadamente */
    }

    .input-group {
        display: flex;
        align-items: center;
        background: rgba(255, 255, 255, 0.15);
        border-radius: 30px;
        padding: 10px 20px;
        margin-bottom: 20px;
        backdrop-filter: blur(2px);
    }

    .input-group .icon {
        margin-right: 10px;
        font-size: 18px;
    }

    .input-group input {
        background: transparent;
        border: none;
        color: white;
        font-size: 16px;
        outline: none;
        width: 100%;
    }

    .input-group input::placeholder {
        color: #d1d5db;
    }

    .login-button {
        background: #ff3e7f;
        border: none;
        color: white;
        font-size: 16px;
        padding: 12px 0;
        width: 100%;
        border-radius: 30px;
        margin-top: 10px;
        cursor: pointer;
        transition: background 0.3s;
    }

    .login-button:hover {
        background: #e63572;
    }

    .links {
        margin-top: 20px;
    }

    .link-item {
        margin-bottom: 10px;
    }

    .link-item a {
        color: #d1d5db;
        font-size: 14px;
        text-decoration: none;
    }

    .link-item a:hover {
        color: white;
        text-decoration: underline;
    }
</style>
