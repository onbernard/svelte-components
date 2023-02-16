<script>
    import { onMount } from "svelte";
    import * as d3 from "d3";
	import AudioPlayer from "$lib/audio/AudioPlayer.svelte";
    import TreeMap from "$lib/d3/TreeMap.svelte";
    import ChordChart from "$lib/d3/ChordChart.svelte";

    let root;
    onMount(async () => {
        await d3.json("/flare.json").then((data) => {
            root = d3.hierarchy(data, (d)=>d.children).sum((d)=>d.size);
        });
    });
</script>


<div class="components">
    <AudioPlayer src="liberee_delivree.mp3" title="La reine des neiges - Libérée, délivrée"/>
    {#if root}
    <TreeMap width=500 height=500 {root}/>
    {/if}
    <ChordChart />
</div>

<style lang="postcss">
    .components {
        display: flex;
        flex-direction: column;
        justify-items: center;
        align-items: center;
    }
</style>