<script>
  import { get_current_component } from "svelte/internal";

  export let width;
  export let mouse;
  export let xScale;
  export let yScale;
  export let monthList;
  export let date_parser;
</script>

{#if mouse.data["date"]}
  <circle
    id="select_circ"
    r="8"
    stroke="black"
    stroke-width="2"
    fill="none"
    z-index="2"
    cx={xScale(mouse.data["date"])}
    cy={yScale(mouse.data["Count"])}
  />
  {#if mouse.data["date"] > date_parser("2021-08-01")}
    <rect
      id="active"
      width="87"
      height="50"
      fill="white"
      z-index="55"
      stroke="blue"
      stroke-width="2"
      x={mouse.x - width * 0.13}
      y={mouse.y}
    />
    <text class="tooltip_text" x={mouse.x - width * 0.12} y={mouse.y + 18}
      >{@html monthList[mouse.data["date"].getMonth()] +
        " " +
        mouse.data["date"].getFullYear()}</text
    >
    <text class="tooltip_subtext" x={mouse.x - width * 0.12} y={mouse.y + 38}
      >{@html mouse.data["Count"] + " permits"}</text
    >
  {:else}
    <rect
      id="active"
      width="87"
      height="50"
      fill="white"
      z-index="55"
      stroke="blue"
      stroke-width="2"
      x={mouse.x + width * 0.02}
      y={mouse.y}
    />
    <text class="tooltip_text" x={mouse.x + width * 0.03} y={mouse.y + 18}
      >{@html monthList[mouse.data["date"].getMonth()] +
        " " +
        mouse.data["date"].getFullYear()}</text
    >
    {#if mouse.data["Count"] == 1}
      <text class="tooltip_subtext" x={mouse.x + width * 0.03} y={mouse.y + 38}
        >{@html mouse.data["Count"] + " permit"}</text
      >
    {:else}
      <text class="tooltip_subtext" x={mouse.x + width * 0.03} y={mouse.y + 38}
        >{@html mouse.data["Count"] + " permits"}</text
      >
    {/if}
  {/if}
{/if}

<style>
  .tooltip_text {
    font-size: 0.8rem;
    font-weight: 700;
  }
  .tooltip_subtext {
    font-size: 0.8rem;
    font-weight: 400;
  }
</style>
