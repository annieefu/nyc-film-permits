<script>
    // export let path;
    export let text;
    export let xScale;
    export let date;
    export let height;
    // export let width;
    export let margin;
    let ypos;



    $: ypos = xScale(date);


    let yPath = `M-0,${height - margin.bottom}H0V0H-0`


</script>


<!-- The line -->

<path stroke="black" z-index="6" d={yPath} fill="none" class="eventFlag" transform="translate({ypos})" />

<!-- The text div -->

{#if text[0][1] == "r"}
<rect class="rect" z-index="6" width="125" height="40" fill="white" stroke="black" transform="translate({ypos})"></rect>
{:else}
<rect class="rect" z-index="6" width="145" height="40" fill="white" stroke="black" transform="translate({ypos})"></rect>
{/if}

    <text class="eventBg" transform="translate({ypos+6}, 18)">{@html text[0]}</text>
    <text class="eventBg" transform="translate({ypos+6}, 30)">{@html text[1]}</text>


<style>
    .eventFlag{
        margin-left: ypos;
        stroke-width: 1.5px;
    }

    .eventBg{
        font-size: .65rem;
    }

    .rect{
        min-width: 200px;
    }


@media (max-width: 400px) {
    .eventBg{
        transform: translate(ypos, 0);
    }

}

</style>