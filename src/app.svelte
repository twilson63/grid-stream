<script>
  import { onMount } from "svelte";
  import { dryrun } from "@permaweb/aoconnect";

  const pid = "03I7E-3wkTZa__Bn1Qq5flYrtEQ7NkcoD9Ctg4o2mNI";
  let items = [];
  onMount(() => {
    setInterval(getGameInfo, 2000);
  });

  window.getGameInfo = async () => {
    const result = await dryrun({
      process: pid,
      Owner: "ukkobWjvi0Gwt7SSt2pdQRS2vXsRO3s-kGDx7jQlJdY",
      tags: [{ name: "Action", value: "GetGameState" }],
      data: "",
    });
    let gameState = JSON.parse(result.Messages[0].Data);
    items = Object.values(gameState.Players);
  };
</script>

<h1 class="text-3xl text-center">Grid View</h1>
<div class="grid grid-cols-40 gap-1">
  {#each items as { x, y }}
    <div
      class="grid-item bg-green-500"
      style="grid-row-start: {y}; grid-column-start: {x};"
    ></div>
  {/each}
</div>

<style>
  .grid-item {
    /* Adjust the width and height as needed */
    width: 20px;
    height: 20px;
  }
</style>
