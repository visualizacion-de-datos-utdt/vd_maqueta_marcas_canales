<script>
  import * as d3 from "d3"
  import { onMount } from "svelte"

  /* Array donde guardaremos la data */
  let deportistas = []

  /* 1. Escala para participaciones */
  let grosorPartic = d3.scaleLinear().range([2, 18])

  /* 2. Escala para genero */
  let colorGenero = d3
    .scaleOrdinal()
    .domain(["F", "M"])
    .range(["#F7DDBA", "#E4D9F2"])

  /* 3. Escala para continentes */
  let colorContinentes = d3
    .scaleOrdinal()
    .domain(["América", "África", "Asia", "Europa", "Oceanía"])
    .range(["#ed334e", "#000000", "#fbb132", "#009fe3", "#00963f"])

  /* 4. Area. Escala para diámetro del círculo */
  let diamMedallas = d3.scaleRadial().range([0, 100])

  onMount(async () => {
    /* Consumimos la data */
    const data = await d3.csv("./data/athletes.csv", d3.autoType)
    console.log("data", data)

    /* Actualizamos dominio con la data de participaciones */
    let minMaxParticipations = d3.extent(data, (d) => d.participations)
    grosorPartic = grosorPartic.domain(minMaxParticipations)

    /* Actualizamos el dominio con la data de las medallas */
    let maxMedallas = d3.max(data, (d) => d.medallas)
    diamMedallas = diamMedallas.domain([0, maxMedallas])

    deportistas = data
  })
</script>

<main>
  <div class="header">
    <img src="/images/logo_referencias.svg" width="190" alt="anillos" />
    <h3 class="headline">
      <b>Los reyes del oro</b>
      Medallas, participaciones y dominio en distintos continentes
    </h3>
    <p class="bajada">
      Los atletas con más medallas olímpicas de oro en los Juegos Olímpicos
    </p>
    <img
      class="referencias"
      src="/images/referencias.svg"
      width="490"
      alt="anillos"
    />
  </div>

  <div class="container">
    <!-- Conedor de las entidades -->
    <div class="container">
      <!-- Iteramos la data para visualizar c/ entidad -->
      {#each deportistas as dep}
        <div class="person-container">
          <div
            class="person"
            style="border-width: {grosorPartic(dep.participations)}px; 
        background-color:{colorGenero(dep.gender)}; 
        width: {diamMedallas(dep.medallas)}px; 
        height: {diamMedallas(dep.medallas)}px; 
        border-color: {colorContinentes(dep.continent)}"
          ></div>
          <p class="nombre">{dep.name}</p>
          <p class="deporte">{dep.sport}</p>
        </div>
      {/each}
    </div>
  </div>
</main>

<style>
  .header {
    display: flex;
    justify-content: center;
    align-items: center;
    flex-direction: column;
    margin-top: 20px;
    margin-bottom: 80px;
  }
  .headline {
    font-size: 40px;
    line-height: 1.2;
    font-weight: normal;
    text-align: center;
    margin: 20px;
  }
  .bajada {
    font-size: 24px;
    font-weight: 300;
    text-align: center;
    margin: 10px;
  }
  .headline b {
    display: block;
  }
  .container {
    display: flex;
    justify-content: center;
    align-items: end;
    margin: auto;
    flex-wrap: wrap;
    max-width: 1020px;
    gap: 30px;
    margin-bottom: 100px;
  }
  .person-container {
    display: flex;
    justify-content: center;
    flex-direction: column;
    align-items: center;
    flex: 180px 0 0;
  }
  .person {
    width: 100px;
    height: 100px;
    border: 10px solid black;
    border-radius: 50%;
    box-sizing: border-box;
    background-color: pink;
  }
  .nombre {
    font-size: 13px;
    font-weight: bold;
    line-height: 1;
    text-transform: uppercase;
    margin: 0;
    margin-top: 8px;
  }
  .deporte {
    font-size: 14px;
    color: #666;
    margin: 0;
  }
  .referencias {
    margin-top: 50px;
    margin-bottom: 20px;
  }
</style>
