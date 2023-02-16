<script>
     import * as d3 from "d3";
    export let height = 800;
    export let width = 800;
    export let colors = d3.scaleOrdinal().range(d3.schemeSet3);
    export let root;

    const treemap = d3.treemap().size([width, height]);
    let tree = treemap(root);

</script>

{#if tree}
<svg {height} {width} >
    {#each tree.leaves() as leaf}
    <g>
        <rect
            x={leaf.x0}
            y={leaf.y0}
            width={Math.max(1,leaf.x1-leaf.x0-1)}
            height={Math.max(1,leaf.y1-leaf.y0-1)}
            fill={colors(leaf.parent.data.name)}/>
        <text lengthAdjust="spacingAndGlyphs" x={leaf.x0} y={(leaf.y1+leaf.y0)/2} textLength={leaf.x1-leaf.x0}>{leaf.data.name}</text>
    </g>
    {/each}
</svg>
{/if}

<style lang="postcss">
    svg {
        @apply bg-stone-100;
    }
</style>