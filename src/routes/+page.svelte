<script lang="ts">
  import { onMount } from "svelte";
  let dataAll = [];
  let refData = [];
  let areas = [];
  let geoDrop: Number;
  let kills: Number;
  let health: Number;
  let areaSel: String;

  let glGeo;
  let glKills;
  let glHealth;

  onMount(async function () {
    const response = await fetch(
      "https://raw.githubusercontent.com/NerfIrelia73/HKWordle/main/src/assets/entries.json"
    );
    dataAll = await response.json();
    dataAll = [...dataAll.entries];
    cleanData();
  });

  function printJournle() {
    console.log(dataAll);
  }

  function cleanData() {
    for (let i = 0; i < dataAll.length; i++) {
      const element = dataAll[i];
      delete element.order;
      if (element.alias[0] === "") {
        delete element.alias;
      }
      if (!areas.includes(element.area)) {
        areas.push(element.area);
      }
      dataAll[i] = element;
    }
    areas = areas;
    refData = JSON.parse(JSON.stringify(dataAll));
  }

  function refreshData() {
    refData = JSON.parse(JSON.stringify(dataAll));
    for (let i = 0; i < refData.length; i++) {
      killsCheck: {
        const element = refData[i];
        if (kills === undefined) {
          break killsCheck;
        }
        if (glKills == 0) {
          if (element.kills === kills) {
            break killsCheck;
          }
        } else if (glKills == 1) {
          if (element.kills < kills) {
            break killsCheck;
          }
        } else if (glKills == 2) {
          if (element.kills > kills) {
            break killsCheck;
          }
        }
        refData.splice(i, 1);
        i--;
      }
    }
    for (let i = 0; i < refData.length; i++) {
      healthCheck: {
        const element = refData[i];
        if (health === undefined) {
          break healthCheck;
        }
        if (glHealth == 0) {
          if (element.health === health) {
            break healthCheck;
          }
        } else if (glHealth == 1) {
          if (element.health < health) {
            break healthCheck;
          }
        } else if (glHealth == 2) {
          if (element.health > health) {
            break healthCheck;
          }
        }
        refData.splice(i, 1);
        i--;
      }
    }
    for (let i = 0; i < refData.length; i++) {
      geoCheck: {
        const element = refData[i];
        if (geoDrop === undefined) {
          break geoCheck;
        }
        if (glGeo == 0) {
          if (element.geo === geoDrop) {
            break geoCheck;
          }
        } else if (glGeo == 1) {
          if (element.geo < geoDrop) {
            break geoCheck;
          }
        } else if (glGeo == 2) {
          if (element.geo > geoDrop) {
            break geoCheck;
          }
        }
        refData.splice(i, 1);
        i--;
      }
    }
    for (let i = 0; i < refData.length; i++) {
      areaCheck: {
        const element = refData[i];
        if (areaSel === "Area") {
          break areaCheck;
        }
        if (element.area === areaSel) {
          break areaCheck;
        }
        refData.splice(i, 1);
        i--;
      }
    }
    refData = refData;
  }
</script>

<svelte:head>
  <title>TableTalk</title>
</svelte:head>

<div class="flex flex-col">
  <div class="flex flex-row m-2 w-full space-x-4 items-center justify-center">
    <div>Search Tools:</div>

    <select class="select input-bordered" bind:value={areaSel}>
      <option disabled selected>Area</option>
      {#each areas as area}
        <option>{area}</option>
      {/each}
    </select>
    <div class="join">
      <select class="select input-bordered join-item" bind:value={glKills}>
        <option value="0">=</option>
        <option value="1">{"<"}</option>
        <option value="2">{">"}</option>
      </select>
      <input
        type="number"
        class="input input-bordered join-item"
        bind:value={kills}
        placeholder="Kills"
      />
    </div>
    <div class="join">
      <select class="select input-bordered join-item" bind:value={glHealth}>
        <option value="0">=</option>
        <option value="1">{"<"}</option>
        <option value="2">{">"}</option>
      </select>
      <input
        type="number"
        class="input input-bordered join-item"
        bind:value={health}
        placeholder="Health"
      />
    </div>
    <div class="join">
      <select class="select input-bordered join-item" bind:value={glGeo}>
        <option value="0">=</option>
        <option value="1">{"<"}</option>
        <option value="2">{">"}</option>
      </select>
      <input
        type="number"
        class="input input-bordered join-item"
        bind:value={geoDrop}
        placeholder="Geo"
      />
    </div>

    <button
      class="btn btn-primary"
      on:click={() => {
        refreshData();
      }}>Search</button
    >
  </div>
  <div class="grid grid-cols-5 justify-items-center gap-4">
    {#each refData as entry}
      <div class="flex flex-col rounded-md bg-base-200 p-2 w-48">
        <div class="flex flex-row whitespace-nowrap">
          {entry.name}
        </div>
        <div class="flex flex-row">
          {entry.area}
        </div>
        <div class="flex flex-row">
          Kills: {entry.kills}
        </div>
        <div class="flex flex-row gap-2">
          <div>
            Geo: {entry.geo}
          </div>
          <div>
            Health: {entry.health}
          </div>
        </div>
        <div class="flex flex-row justify-center items-center h-full">
          <img src={entry.url} alt={entry.name} />
        </div>
      </div>
    {/each}
  </div>
  <div class="flex flex-row justify-center my-2">
    <button
      class="btn btn-secondary"
      on:click={() => {
        printJournle();
      }}
    >
      Log Tester
    </button>
  </div>
</div>
