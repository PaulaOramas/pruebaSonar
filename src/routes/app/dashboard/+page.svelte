<script>
  import { onMount } from 'svelte';

  let productsChart;
  let usersChart;

  // Redirección si no es admin
  onMount(() => {
    if (localStorage.getItem("isAdmin") !== "true") {
      window.location.href = "/";
    }
    cargarProductosYGraficar();
    cargarUsuariosYGraficar();
  });

  function generarColoresPastel(num) {
    const colores = [];
    for (let i = 0; i < num; i++) {
      const r = Math.floor(150 + Math.random() * 100);
      const g = Math.floor(150 + Math.random() * 100);
      const b = Math.floor(150 + Math.random() * 100);
      colores.push(`rgb(${r},${g},${b})`);
    }
    return colores;
  }

  async function cargarProductosYGraficar() {
    const ctx = productsChart.getContext('2d');
    try {
      const response = await fetch('https://eltragolocorest.runasp.net/api/Categoria/CantidadProductosPorCategoria');
      if (!response.ok) throw new Error('Error al obtener datos');
      const data = await response.json();
      const labels = data.map(item => item.CAT_NOMBRE);
      const cantidades = data.map(item => item.CANTIDAD_PRODUCTOS);
      const backgroundColors = generarColoresPastel(labels.length);

      new window.Chart(ctx, {
        type: 'bar',
        data: {
          labels: labels,
          datasets: [{
            label: 'Cantidad de Productos',
            data: cantidades,
            backgroundColor: backgroundColors,
            borderColor: '#d4af37',
            borderWidth: 1
          }]
        },
        options: {
          responsive: true,
          plugins: {
            legend: { labels: { color: '#f0db7d' } }
          },
          scales: {
            x: { ticks: { color: '#f0db7d' }, grid: { color: 'rgba(255,255,255,0.1)' } },
            y: { beginAtZero: true, ticks: { color: '#f0db7d' }, grid: { color: 'rgba(255,255,255,0.1)' } }
          }
        }
      });
    } catch (error) {
      console.error('Error cargando datos para gráfico:', error);
    }
  }

  async function cargarUsuariosYGraficar() {
    const ctx = usersChart.getContext('2d');
    try {
      const response = await fetch('https://eltragolocorest.runasp.net/api/Login/RolesCantidadUsuarios');
      if (!response.ok) throw new Error('Error al obtener datos');
      const data = await response.json();
      const labels = data.map(item => item.LOG_ROL);
      const cantidades = data.map(item => item.CANTIDAD_USUARIOS);
      const backgroundColor = ['#f0db7d', '#d4af37', '#c1b24a', '#e2d77a'];

      new window.Chart(ctx, {
        type: 'doughnut',
        data: {
          labels: labels,
          datasets: [{
            data: cantidades,
            backgroundColor: backgroundColor.slice(0, labels.length),
            borderColor: '#2c2f36',
            borderWidth: 2
          }]
        },
        options: {
          responsive: true,
          plugins: {
            legend: {
              position: 'top',
              labels: { color: '#f0db7d' }
            }
          }
        }
      });
    } catch (error) {
      console.error('Error cargando datos para gráfico:', error);
    }
  }
</script>

<svelte:head>
  <title>Panel Admin - Trago Loco</title>
  <link href="https://fonts.googleapis.com/css2?family=Montserrat:wght@400;600&display=swap" rel="stylesheet" />
  <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" />
  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
</svelte:head>

<main>
  <div class="titulo">
    <h1>⚙️ Panel de Administración</h1>
    <p>Accede a la Gestión de Productos, Usuarios y Facturas</p>
  </div>

  <div class="panel">
    <!-- Navegación -->
    <div class="nav">
      <a href="/app/productos">🥂 Productos</a>
      <a href="/app/usuarios">🧑‍🤝‍🧑 Usuarios</a>
      <a href="/app/facturas">📋 Facturas</a>
    </div>

    <!-- Estadísticas -->
    <div class="stats-section">
      <h2>📈 Estadísticas de Gestión</h2>
      <div class="charts">
        <div class="chart-card">
          <h3>🍹 Productos Registrados</h3>
          <canvas bind:this={productsChart} width="600" height="340"></canvas>
        </div>
        <div class="chart-card">
          <h3>🧑‍🤝‍🧑 Distribución de Usuarios</h3>
          <canvas bind:this={usersChart} width="600" height="340"></canvas>
        </div>
      </div>
    </div>
  </div>
</main>

<style>
  main {
    min-height: 100vh;
    background: #222;
    color: #f0db7d;
    font-family: 'Montserrat', sans-serif;
    padding-bottom: 40px;
  }

  .titulo {
    text-align: center;
    margin: 20px 0 40px 0;
  }

  .panel {
    max-width: 960px;
    margin: 0 auto 40px auto;
    padding: 10px 20px 40px 20px;
    background: #23252b;
    border-radius: 1rem;
    box-shadow: 0 6px 20px rgba(0,0,0,0.2);
  }

  .nav {
    display: flex;
    justify-content: center;
    gap: 40px;
    margin: 40px auto 50px auto;
    max-width: 700px;
    flex-wrap: wrap;
    z-index: 2;
    position: relative;
  }

  .nav a {
    display: inline-block;
    background-color: rgba(212, 175, 55, 0.15);
    color: #f0db7d;
    text-decoration: none;
    font-weight: 700;
    font-size: 1.3rem;
    padding: 14px 30px;
    border-radius: 12px;
    box-shadow: 0 2px 8px rgba(212, 175, 55, 0.3);
    transition:
      background-color 0.3s ease,
      color 0.3s ease,
      box-shadow 0.3s ease,
      transform 0.2s ease;
    white-space: nowrap;
    cursor: pointer;
    user-select: none;
  }

  .nav a:hover,
  .nav a:focus {
    background-color: #d4af37;
    color: #222;
    box-shadow: 0 4px 16px rgba(212, 175, 55, 0.8);
    transform: scale(1.05);
    outline: none;
  }

  @media (max-width: 480px) {
    .nav {
      max-width: 100%;
      gap: 30px;
      padding: 20px 15px;
    }

    .nav a {
      font-size: 1.15rem;
      padding: 14px 20px;
    }
  }

  .stats-section {
    background-color: #2c2f36;
    padding: 25px 30px 35px 30px;
    border-radius: 10px;
    box-shadow: 0 0 15px #d4af37cc;
  }

  .stats-section h2 {
    text-align: center;
    margin-bottom: 35px;
    font-weight: 700;
    font-size: 1.8rem;
    letter-spacing: 1px;
  }

  .charts {
    display: flex;
    flex-direction: row; /* Cambiado de column a row */
    gap: 50px;
    justify-content: center; /* Opcional: centra los gráficos horizontalmente */
    flex-wrap: wrap; /* Opcional: permite que se acomoden en pantallas pequeñas */
  }

  .chart-card {
    background-color: #3b3f46;
    padding: 25px 20px 30px 20px;
    border-radius: 10px;
    box-shadow: 0 0 20px #d4af37bb;
  }

  .chart-card h3 {
    text-align: center;
    margin-bottom: 24px;
    font-weight: 600;
    font-size: 1.4rem;
  }

  canvas {
    display: block;
    max-width: 100%;
    width: 100%;
    height: 340px !important;
    margin: 0 auto;
  }
</style>