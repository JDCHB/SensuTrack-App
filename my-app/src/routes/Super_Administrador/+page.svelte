<script>
    // Mantengo exactamente los mismos imports y funciones del script original
    import RegistroUSUAdmin from "../../lib/components/Admin/RegistroUsu.svelte";
    import Reportes from "../../lib/components/Admin/Reportes.svelte";
    import RegistroGPS from "../../lib/components/Admin/RegistroGPS.svelte";
    import TablaUsuarios from "../../lib/components/Admin/TablaUsuarios.svelte";
    import TablaCiegos from "../../lib/components/Admin/TablaCiegos.svelte";
    import RegistrarRoles from "../../lib/components/Admin/RegistroRoles.svelte";
    import RegistroModulos from "$lib/components/Admin/RegistroModulos.svelte";
    import PowerBi from "$lib/components/Admin/PowerBI.svelte";
    import HistorialUbi from "$lib/components/Admin/HistorialUbi.svelte";
    import ModuloxRol from "$lib/components/Admin/ModuloxRol.svelte";

    let expandido = false;
    let activeSection = "X";
    let registerLoader;
    let menuAbierto = false;

    function showLoader() {
        registerLoader.style.display = "flex";
    }

    function hideLoader() {
        registerLoader.style.display = "none";
    }

    function toggleMenu() {
        menuAbierto = !menuAbierto;
    }

    function logout() {
        localStorage.clear();
        window.location.href = "/";
    }

    // Acciones disponibles (igual que antes)
    function mostrarConfirmacionRegistroUsuario() {
        activeSection = "registro_usuario";
        menuAbierto = false;
    }
    function mostrarConfirmacionReporte() {
        activeSection = "reportes";
        menuAbierto = false;
    }
    function mostrarConfirmacionRegistroGPS() {
        activeSection = "registro_gps";
        menuAbierto = false;
    }
    function mostrarTablaUsuarios() {
        activeSection = "tabla_usuarios";
        menuAbierto = false;
    }
    function mostrarTablaCiegos() {
        activeSection = "tabla_discapacitados";
        menuAbierto = false;
    }
    function mostrarRegistroRoles() {
        activeSection = "registro_roles";
        menuAbierto = false;
    }
    function mostrarRegistroModulos() {
        activeSection = "registro_modulos";
        menuAbierto = false;
    }
    function mostrarRegistroModuloxRol() {
        activeSection = "modulos_por_rol";
        menuAbierto = false;
    }
    function mostrarTableroPowerBI() {
        activeSection = "tablero";
        menuAbierto = false;
    }
    function mostrarHistorialUbicaciones() {
        activeSection = "historial_ubicaciones";
        menuAbierto = false;
    }
</script>

<div class="mobile-layout">
    <!-- Barra superior móvil -->
    <header class="mobile-header">
        <button
            class="menu-button"
            on:click={toggleMenu}
            aria-label={menuAbierto ? "Cerrar menú" : "Abrir menú"}
        >
            <i class="bi bi-list"></i>
        </button>
        <h1>Admin</h1>
        <div class="header-spacer"></div>
    </header>

    <!-- Menú lateral móvil -->
    <div class="mobile-sidebar {menuAbierto ? 'open' : ''}">
        <nav>
            <button on:click={mostrarConfirmacionRegistroUsuario}>
                <i class="bi bi-person-add"></i>
                <span>Registrar Usuario</span>
            </button>
            <button on:click={mostrarConfirmacionReporte}>
                <i class="bi bi-file-earmark-spreadsheet"></i>
                <span>Reportes</span>
            </button>
            <button on:click={mostrarConfirmacionRegistroGPS}>
                <i class="bi bi-geo-alt"></i>
                <span>Registrar GPS</span>
            </button>
            <button on:click={mostrarTablaUsuarios}>
                <i class="bi bi-table"></i>
                <span>Tabla Usuarios</span>
            </button>
            <button on:click={mostrarTablaCiegos}>
                <i class="bi bi-table"></i>
                <span>Tabla Discapacitados</span>
            </button>
            <button on:click={mostrarRegistroRoles}>
                <i class="bi bi-person-badge"></i>
                <span>Registrar Roles</span>
            </button>
            <button on:click={mostrarRegistroModulos}>
                <i class="bi bi-stack"></i>
                <span>Registrar Módulos</span>
            </button>
            <button on:click={mostrarRegistroModuloxRol}>
                <i class="bi bi-shield-lock"></i>
                <span>Asignar Módulo</span>
            </button>
            <button on:click={mostrarTableroPowerBI}>
                <i class="bi bi-graph-up"></i>
                <span>Tablero</span>
            </button>
            <button on:click={mostrarHistorialUbicaciones}>
                <i class="bi bi-map"></i>
                <span>Ubicaciones</span>
            </button>
            <button on:click={logout} class="logout">
                <i class="bi bi-power"></i>
                <span>Cerrar Sesión</span>
            </button>
        </nav>
    </div>

    {#if menuAbierto}
        <button class="overlay" on:click={toggleMenu} aria-label="Cerrar menú"
        ></button>
    {/if}

    <!-- Contenido principal -->
    <main class="mobile-content">
        {#if activeSection === "X"}
            <div class="admin-welcome">
                <h1>👋 ¡Bienvenido, Administrador!</h1>
                <p class="subtitle">
                    Este panel te permite tener el control total del sistema
                    para apoyar a las personas ciegas con eficiencia y
                    seguridad.
                </p>
            </div>
        {:else if activeSection === "registro_usuario"}
            <RegistroUSUAdmin
                on:showLoader={showLoader}
                on:hideLoader={hideLoader}
            ></RegistroUSUAdmin>
        {:else if activeSection === "reportes"}
            <Reportes></Reportes>
        {:else if activeSection === "registro_gps"}
            <RegistroGPS on:showLoader={showLoader} on:hideLoader={hideLoader}
            ></RegistroGPS>
        {:else if activeSection === "tabla_usuarios"}
            <TablaUsuarios></TablaUsuarios>
        {:else if activeSection === "tabla_discapacitados"}
            <TablaCiegos></TablaCiegos>
        {:else if activeSection === "registro_roles"}
            <RegistrarRoles
                on:showLoader={showLoader}
                on:hideLoader={hideLoader}
            ></RegistrarRoles>
        {:else if activeSection === "registro_modulos"}
            <RegistroModulos
                on:showLoader={showLoader}
                on:hideLoader={hideLoader}
            ></RegistroModulos>
        {:else if activeSection === "modulos_por_rol"}
            <ModuloxRol></ModuloxRol>
        {:else if activeSection === "tablero"}
            <PowerBi></PowerBi>
        {:else if activeSection === "historial_ubicaciones"}
            <HistorialUbi></HistorialUbi>
        {/if}

        <!-- Loader (se mantiene igual) -->
        <div class="loader-container" bind:this={registerLoader}>
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
    </main>
</div>

<link
    href="https://cdn.jsdelivr.net/npm/bootstrap-icons@1.10.0/font/bootstrap-icons.css"
    rel="stylesheet"
/>

<style>
    /* Estilos generales para móvil */
    :global(body) {
        margin: 0;
        padding: 0;
        font-family: "Segoe UI", Roboto, Oxygen, Ubuntu, Cantarell, sans-serif;
        -webkit-tap-highlight-color: transparent;
    }

    .mobile-layout {
        display: flex;
        flex-direction: column;
        height: 100vh;
        width: 100vw;
        position: relative;
        overflow: hidden;
        background: #f8f9fa;
    }

    /* Barra superior - Colores vibrantes */
    .mobile-header {
        display: flex;
        align-items: center;
        padding: 0.75rem 1rem;
        background: linear-gradient(135deg, #6e48aa 0%, #9d50bb 100%);
        color: white;
        height: 60px;
        box-shadow: 0 2px 10px rgba(110, 72, 170, 0.3);
        z-index: 10;
        position: relative;
    }

    .menu-button {
        background: rgba(255, 255, 255, 0.2);
        border: none;
        border-radius: 8px;
        color: white;
        font-size: 1.5rem;
        margin-right: 1rem;
        padding: 0.5rem 0.75rem;
        transition: all 0.3s ease;
    }

    .menu-button:hover {
        background: rgba(255, 255, 255, 0.3);
        transform: scale(1.05);
    }

    .mobile-header h1 {
        font-size: 1.4rem;
        margin: 0;
        font-weight: 600;
        text-shadow: 0 1px 2px rgba(0, 0, 0, 0.1);
    }

    .header-spacer {
        flex: 1;
    }

    /* Menú lateral - Diseño moderno */
    .mobile-sidebar {
        position: fixed;
        top: 60px;
        left: -280px;
        width: 280px;
        height: calc(100vh - 60px);
        background: linear-gradient(180deg, #4a00e0 0%, #8e2de2 100%);
        color: white;
        transition: transform 0.3s cubic-bezier(0.4, 0, 0.2, 1);
        z-index: 20;
        overflow-y: auto;
        padding: 1rem 0;
    }

    .mobile-sidebar.open {
        transform: translateX(280px);
    }

    .mobile-sidebar nav {
        display: flex;
        flex-direction: column;
        gap: 0.5rem;
        padding: 0 0.5rem;
    }

    .mobile-sidebar button {
        display: flex;
        align-items: center;
        padding: 0.8rem 1rem;
        color: white;
        background: rgba(255, 255, 255, 0.1);
        border: none;
        border-radius: 8px;
        text-align: left;
        font-size: 1rem;
        font-weight: 500;
        transition: all 0.2s ease;
        margin: 0 0.5rem;
    }

    .mobile-sidebar button i {
        margin-right: 1rem;
        font-size: 1.2rem;
        width: 24px;
        text-align: center;
        color: rgba(255, 255, 255, 0.9);
    }

    .mobile-sidebar button:hover {
        background: rgba(255, 255, 255, 0.2);
        transform: translateX(5px);
    }

    .mobile-sidebar button:active {
        transform: scale(0.98);
    }

    .mobile-sidebar .logout {
        background: rgba(255, 75, 75, 0.2);
        color: #ffd6d6;
        margin-top: auto;
    }

    .mobile-sidebar .logout:hover {
        background: rgba(255, 75, 75, 0.3);
    }

    /* Overlay */
    .overlay {
        position: fixed;
        top: 60px;
        left: 0;
        right: 0;
        bottom: 0;
        background-color: rgba(0, 0, 0, 0.5);
        z-index: 15;
        border: none;
        padding: 0;
        margin: 0;
        width: 100%;
        height: 100%;
        cursor: pointer;
        backdrop-filter: blur(2px);
        transition: opacity 0.3s ease;
    }

    /* Contenido principal - Fondo moderno */
    .mobile-content {
        flex: 1;
        overflow-y: auto;
        padding: 1.2rem;
        background: #f8f9fa;
    }

    /* Loader (se mantiene igual) */
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

    /* Mensaje de bienvenida - Tarjeta moderna */
    .admin-welcome {
        background: white;
        border-radius: 16px;
        padding: 1.8rem;
        box-shadow: 0 4px 20px rgba(110, 72, 170, 0.15);
        margin: 1rem 0;
        border: 1px solid rgba(110, 72, 170, 0.1);
        text-align: center;
    }

    .admin-welcome h1 {
        font-size: 1.7rem;
        margin-bottom: 1.2rem;
        color: #4a00e0;
        background: linear-gradient(90deg, #6e48aa, #9d50bb);
        -webkit-background-clip: text;
        background-clip: text;
        color: transparent;
    }

    .admin-welcome .subtitle {
        font-size: 1.05rem;
        color: #5a5a5a;
        line-height: 1.6;
    }

    /* Efectos hover para botones */
    button {
        cursor: pointer;
        transition: all 0.2s ease;
    }

    /* Ajustes para pantallas más grandes */
    @media (min-width: 768px) {
        .mobile-sidebar {
            width: 300px;
            left: -300px;
            background: linear-gradient(180deg, #4a00e0 0%, #8e2de2 100%);
        }

        .mobile-sidebar.open {
            transform: translateX(300px);
        }

        .admin-welcome {
            padding: 2.5rem;
            max-width: 650px;
            margin: 2rem auto;
        }

        .mobile-content {
            padding: 1.5rem 2rem;
        }
    }

    /* Scrollbar personalizada */
    .mobile-sidebar::-webkit-scrollbar {
        width: 6px;
    }

    .mobile-sidebar::-webkit-scrollbar-track {
        background: rgba(255, 255, 255, 0.05);
    }

    .mobile-sidebar::-webkit-scrollbar-thumb {
        background: rgba(255, 255, 255, 0.2);
        border-radius: 3px;
    }
</style>
