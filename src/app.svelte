<script>
  import { onMount } from "svelte";
  import { dryrun } from "@permaweb/aoconnect";

  const pid = "03I7E-3wkTZa__Bn1Qq5flYrtEQ7NkcoD9Ctg4o2mNI";
  let items = [];
  onMount(() => {
    let data = [];
    for (var row = 1; row <= 40; row++) {
      for (var col = 1; col <= 40; col++) {
        data.push({ x: row, y: col, active: false });
      }
    }
    items = data;
    setTimeout(getGameInfo, 2000);
  });

  window.getGameInfo = async () => {
    const result = await dryrun({
      process: pid,
      Owner: "ukkobWjvi0Gwt7SSt2pdQRS2vXsRO3s-kGDx7jQlJdY",
      tags: [{ name: "Action", value: "GetGameState" }],
      data: "",
    });
    let gameState = JSON.parse(result.Messages[0].Data);
    console.log(gameState);
    items = items.map((c) => {
      c.active = false;
      c.health = 0;
      return c;
    });
    Object.values(gameState.Players).forEach((p) => {
      items = items.map((c) => {
        if (c.x === p.x && c.y === p.y) {
          c.active = true;
          c.health = p.health;
        }
        return c;
      });
    });
    setTimeout(getGameInfo, 2000);
  };

  function showCell(active, health) {
    if (active) {
      if (health > 50) {
        return "bg-green-500";
      } else {
        return "bg-red-200";
      }
    } else {
      return "";
    }
  }
</script>

<h1 class="text-3xl text-center">Grid View</h1>
<div class="flex items-center justify-center h-screen">
  <div class="grid grid-cols-40 w-2/3">
    {#each items as { x, y, active, health }}
      <div
        class="grid-item border-1 {showCell(active, health)}"
        style="grid-row-start: {y}; grid-column-start: {x};"
      ></div>
    {/each}
  </div>
</div>

<style>
  .grid-item {
    /* Adjust the width and height as needed */
    width: 30px;
    height: 30px;
  }
</style>
