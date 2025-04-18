<script>
    import { onMount } from "svelte";

    let todos = [];
    let loading = false;
    let error = null;
    let opcion;
    let fecha_de = "";
    let fecha_hasta = "";
    let mostrarReporte = false;

    async function generar() {
        try {
            loading = true;
            mostrarReporte = false;
            error = null;

            opcion = document.getElementById("opcion").value;
            fecha_de = document.getElementById("desde_ciegos").value;
            fecha_hasta = document.getElementById("hasta_ciegos").value;

            console.log({
                fecha1: fecha_de,
                fecha2: fecha_hasta,
            });

            if (!fecha_de || !fecha_hasta) {
                Swal.fire({
                    icon: "error",
                    title: "Error",
                    text: "Por favor, selecciona ambas fechas.",
                });
                return;
            }

            const response = await fetch(
                "https://proyectomascotas.onrender.com/Ciegos_Report",
                {
                    method: "POST",
                    headers: {
                        "Content-Type": "application/json",
                    },
                    body: JSON.stringify({
                        fecha1: fecha_de,
                        fecha2: fecha_hasta,
                    }),
                },
            );

            if (!response.ok) throw new Error("Error al cargar los datos");
            const data = await response.json();
            console.log(data);

            todos = data.resultado;
            mostrarReporte = true;
            setTimeout(() => globalThis.$("#myTable").DataTable(), 0);
        } catch (e) {
            error = e.message;
            console.error("Error al generar reporte:", e);
        } finally {
            loading = false;
        }
    }

    function exportarPDF() {
        const { jsPDF } = window.jspdf;
        const pdf = new jsPDF();

        const columns = [
            "Id",
            "Nombre",
            "Género",
            "Tipo de Ceguera",
            "Cuidador",
            "Fecha",
            "Estado",
        ];

        const body = todos.map((todo) => [
            todo.id,
            todo.nombre,
            todo.genero,
            todo.tipo_ceguera,
            todo.nombre_cuidador,
            todo.fecha,
            todo.estado,
        ]);

        pdf.text(
            20,
            20,
            "Reporte de discapacitados visuales registrados en la página",
        );
        pdf.autoTable(columns, body, { startY: 30 });
        pdf.save("ReporteDiscapacitados.pdf");
        Swal.fire({
            position: "center",
            icon: "success",
            title: "Se exportó de manera exitosa",
            showConfirmButton: false,
            timer: 1500,
        });
    }
</script>

<div class="report-container">
    <h2 class="report-title">Reportes</h2>

    <div class="report-section">
        <select class="vibrant-select" id="opcion" required>
            <option value="1">Discapacitados</option>
            <option value="2">Usuarios</option>
            <option value="3">Otro</option>
        </select>
    </div>

    <div class="date-range">
        <div class="date-input">
            <label for="desde_ciegos" class="vibrant-label">Desde:</label>
            <input type="date" id="desde_ciegos" class="vibrant-date" />
        </div>
        <div class="date-input">
            <label for="hasta_ciegos" class="vibrant-label">Hasta:</label>
            <input type="date" id="hasta_ciegos" class="vibrant-date" />
        </div>
    </div>

    <div class="report-action">
        <button class="vibrant-button" on:click={generar}>
            <i class="bi bi-file-earmark-text"></i> Generar Reporte
        </button>
    </div>
</div>

<!-- Mostrar Datos -->
<div class="report-results" id="MostrarReporte">
    {#if loading}
        <div class="vibrant-loader">
            <div class="loader-bar"></div>
        </div>
    {:else if error}
        <p class="error-message">Error: {error}</p>
    {:else if mostrarReporte}
        <h3 class="results-title">Discapacitados Registrados</h3>
        <div class="table-container">
            <table class="vibrant-table">
                <thead>
                    <tr>
                        <th>Id</th>
                        <th>Nombre</th>
                        <th>Género</th>
                        <th>Tipo de Ceguera</th>
                        <th>Cuidador</th>
                        <th>Fecha y Hora</th>
                        <th>Estado</th>
                    </tr>
                </thead>
                <tbody>
                    {#each todos as todo}
                        <tr>
                            <td>{todo.id}</td>
                            <td>{todo.nombre}</td>
                            <td>{todo.genero}</td>
                            <td>{todo.tipo_ceguera}</td>
                            <td>{todo.nombre_cuidador}</td>
                            <td>{todo.fecha}</td>
                            <td>
                                <span
                                    class="status-{todo.estado
                                        ? 'active'
                                        : 'inactive'}"
                                >
                                    {todo.estado ? "Activo" : "Inactivo"}
                                </span>
                            </td>
                        </tr>
                    {/each}
                </tbody>
            </table>
        </div>
        <div class="export-action">
            <button class="export-button" on:click={exportarPDF}>
                <i class="bi bi-download"></i> Exportar como PDF
            </button>
        </div>
    {/if}
</div>

<link
    href="https://cdn.jsdelivr.net/npm/bootstrap-icons@1.10.0/font/bootstrap-icons.css"
    rel="stylesheet"
/>

<style>
    /* ESTILOS VIBRANTES PARA REPORTES */
    .report-container {
        max-width: 960px;
        margin: 2rem auto;
        padding: 2rem;
        background: white;
        border-radius: 16px;
        box-shadow: 0 8px 30px rgba(110, 72, 170, 0.15);
    }

    .report-title {
        text-align: center;
        margin-bottom: 2rem;
        font-size: 2rem;
        font-weight: 700;
        background: linear-gradient(90deg, #6e48aa, #9d50bb);
        -webkit-background-clip: text;
        background-clip: text;
        color: transparent;
    }

    .vibrant-select {
        width: 100%;
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

    .date-range {
        display: grid;
        grid-template-columns: 1fr 1fr;
        gap: 1.5rem;
        margin: 1.5rem 0;
    }

    .date-input {
        display: flex;
        flex-direction: column;
    }

    .vibrant-label {
        font-weight: 600;
        margin-bottom: 0.5rem;
        color: #5a5a5a;
    }

    .vibrant-date {
        padding: 1rem;
        font-size: 1rem;
        border: 2px solid #e0e0e0;
        border-radius: 10px;
        background: #f8f9fa;
        transition: all 0.3s ease;
    }

    .vibrant-date:focus {
        border-color: #9d50bb;
        box-shadow: 0 0 0 3px rgba(157, 80, 187, 0.2);
        outline: none;
        background: white;
    }

    .report-action {
        text-align: center;
        margin-top: 2rem;
    }

    .vibrant-button {
        background: linear-gradient(135deg, #6e48aa 0%, #9d50bb 100%);
        color: white;
        border: none;
        padding: 1rem 2rem;
        font-size: 1.1rem;
        font-weight: 600;
        border-radius: 10px;
        cursor: pointer;
        transition: all 0.3s ease;
        box-shadow: 0 4px 15px rgba(110, 72, 170, 0.3);
        display: inline-flex;
        align-items: center;
        gap: 0.5rem;
    }

    .vibrant-button:hover {
        transform: translateY(-3px);
        box-shadow: 0 6px 20px rgba(110, 72, 170, 0.4);
    }

    .vibrant-button:active {
        transform: translateY(-1px);
    }

    /* RESULTADOS */
    .report-results {
        max-width: 960px;
        margin: 2rem auto;
        padding: 2rem;
        background: white;
        border-radius: 16px;
        box-shadow: 0 8px 30px rgba(110, 72, 170, 0.15);
    }

    .vibrant-loader {
        height: 5px;
        background: #f0f0f0;
        border-radius: 5px;
        overflow: hidden;
    }

    .loader-bar {
        height: 100%;
        width: 100%;
        background: linear-gradient(90deg, #6e48aa, #9d50bb, #4776e6, #8e54e9);
        background-size: 400% 400%;
        animation: gradient 2s ease infinite;
        border-radius: 5px;
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

    .error-message {
        text-align: center;
        color: #ff4757;
        font-weight: 500;
        padding: 1rem;
    }

    .results-title {
        text-align: center;
        margin-bottom: 2rem;
        color: #6e48aa;
        font-size: 1.8rem;
        font-weight: 600;
    }

    .table-container {
        overflow-x: auto;
    }

    .vibrant-table {
        width: 100%;
        border-collapse: separate;
        border-spacing: 0;
        border-radius: 10px;
        overflow: hidden;
        box-shadow: 0 2px 10px rgba(0, 0, 0, 0.05);
    }

    .vibrant-table thead {
        background: linear-gradient(135deg, #6e48aa 0%, #9d50bb 100%);
        color: white;
    }

    .vibrant-table th {
        padding: 1rem;
        text-align: left;
        font-weight: 600;
    }

    .vibrant-table td {
        padding: 0.8rem 1rem;
        border-bottom: 1px solid #f0f0f0;
    }

    .vibrant-table tbody tr:nth-child(even) {
        background-color: #f9f9f9;
    }

    .vibrant-table tbody tr:hover {
        background-color: #f0e6ff;
    }

    .status-active {
        color: #2ecc71;
        font-weight: 500;
    }

    .status-inactive {
        color: #e74c3c;
        font-weight: 500;
    }

    .export-action {
        text-align: center;
        margin-top: 2rem;
    }

    .export-button {
        background: linear-gradient(135deg, #2ecc71 0%, #27ae60 100%);
        color: white;
        border: none;
        padding: 1rem 2rem;
        font-size: 1.1rem;
        font-weight: 600;
        border-radius: 10px;
        cursor: pointer;
        transition: all 0.3s ease;
        box-shadow: 0 4px 15px rgba(46, 204, 113, 0.3);
        display: inline-flex;
        align-items: center;
        gap: 0.5rem;
    }

    .export-button:hover {
        transform: translateY(-3px);
        box-shadow: 0 6px 20px rgba(46, 204, 113, 0.4);
    }

    .export-button:active {
        transform: translateY(-1px);
    }

    /* RESPONSIVE */
    @media (max-width: 768px) {
        .date-range {
            grid-template-columns: 1fr;
            gap: 1rem;
        }

        .report-container,
        .report-results {
            padding: 1.5rem;
            margin: 1rem;
        }

        .report-title,
        .results-title {
            font-size: 1.5rem;
        }

        .vibrant-button,
        .export-button {
            padding: 0.9rem 1.5rem;
            font-size: 1rem;
        }
    }
</style>
