<script>
  import * as d3 from "d3"
  import {onMount} from "svelte"

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
  let diametroTotal = d3.scaleRadial().range([0, 100])

  onMount(async () => {

    /* Consumimos la data */
    const data = await d3.csv("./data/athletes.csv", d3.autoType)
    console.log("data", data)

    /* Actualizamos dominio con la data de participaciones */
    let minMaxParticipations = d3.extent(data, d => d.participations)
    grosorPartic = grosorPartic.domain(minMaxParticipations)

    /* Actualizamos el dominio con la data de total de medallas */
    let maxTotal = d3.max(data, d => d.total)
    diametroTotal = diametroTotal.domain([0, maxTotal])

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
    <img class="referencias" src="/images/referencias.svg" width="490" alt="anillos" />
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
        width: {diametroTotal(dep.total)}px; 
        height: {diametroTotal(dep.total)}px; 
        border-color: {colorContinentes(dep.continent)}"
          ></div>

          <div class="medals-wrapper">
            {#if dep.gold > 0}
              <div class="medal gold">{dep.gold}</div>
            {/if}
            {#if dep.silver > 0}
              <div class="medal silver">{dep.silver}</div>
            {/if}
            {#if dep.bronze > 0}
              <div class="medal bronze">{dep.bronze}</div>
            {/if}
          </div>

          <p class="atleta">
            <b class="name">{dep.name}</b>
            <br />
            {dep.country}
            <br />
            {dep.sport}
          </p>
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

  .medals-wrapper {
    display: flex;
    justify-content: center;
    align-items: center;
    gap: 5px;
  }
  .medal {
    width: 20px;
    height: 20px;
    border-radius: 50%;
    margin: 5px 0;
    font-size: 12px;
    font-weight: bold;
    line-height: 1;
    display: flex;
    justify-content: center;
    align-items: center;
  }
  .gold {
    background-color: gold;
  }
  .silver {
    background-color: silver;
  }
  .bronze {
    background-color: #e08585;
  }
  .atleta {
    font-size: 13px;
    line-height: 1.3;
    color: rgb(65, 65, 65);
    font-weight: normal;
    text-align: center;
    margin-top: 5px;
  }
  .name {
    text-transform: uppercase;
  }
  .referencias {
    margin-top: 50px;
    margin-bottom: 20px;
  }
</style>
