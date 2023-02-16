<script>
    // CREDIT https://www.visualcinnamon.com/2016/06/orientation-gradient-d3-chord-diagram/
    // https://gist.github.com/nbremer/864b11eb83aac3a1f6a2
    import * as d3 from 'd3'
    import { onMount } from 'svelte';
    import { customChordLayout } from './customChord'

    export let width = 800;
    export let height = 900;
    export let adjacency = [
        [0,4,3,2,5,2], //Black Widow
        [4,0,3,2,4,3], //Captain America
        [3,3,0,2,3,3], //Hawkeye
        [2,2,2,0,3,3], //The Hulk
        [5,4,3,3,0,2], //Iron Man
        [2,3,3,3,2,0], //Thor
    ];
    export let names = ["Black Widow","Captain America","Hawkeye","The Hulk","Iron Man","Thor"];
    export let colors = ["#301E1E", "#083E77", "#342350", "#567235", "#8B161C", "#DF7C00"];

    const margin = { top: 0, right: 20, bottom: 20, left: 115 };
    const innerHeight = height - margin.top - margin.bottom;
    const innerWidth = width - margin.left - margin.right;
    
    let svgElem;
    $: viewBx = [-width / 2.5, -height / 1.8, width * 1.1, height * 1.1];
    $: outerRadius = Math.min(width, height) * 0.5 - 60;
    $: innerRadius = outerRadius - 10;
    $: chordGen = customChordLayout()
        .padding(.15)
        .sortChords(d3.descending) 
        .matrix(adjacency);
    
    $: arcGen = d3.arc()
        .innerRadius(innerRadius)
        .outerRadius(outerRadius)
    
    $: ribbonGen = d3.ribbon()
        .radius(innerRadius-1)
        .padAngle(1/innerRadius)
    const getGradID = (d) => ("linkGrad-" + d.source.index + "-" + d.target.index)
</script>

<svg viewBox={viewBx} {width} {height} bind:this={svgElem}>
    <g transform={`translate(${margin.left},${margin.top})`}>
        {#each chordGen.groups() as group, i}
        <!--Outer arcs-->
        <path fill={colors[i]} d={arcGen(group)}/>
        <!--Arc labels-->
        <g style="transform:rotate({((group.endAngle+group.startAngle) * 90) / Math.PI -90}deg) translate({outerRadius}px,0);">
            <text 
                fill={colors[i]} 
                class="group-label"
                font-weight="bold"
                dx="25"
                style="text-anchord:middle;transform:{group.startAngle+group.endAngle > 2*Math.PI? 'rotate(180deg) translate(-95px)': null}"
            >{names[i]}</text>
        </g>
        {/each}
        {#each chordGen.chords() as chord, i}
        <!--Gradient definition-->
        <defs>
            <linearGradient
                id={getGradID(chord)}
                gradientUnits="userSpaceOnUse"
                x1={innerRadius*Math.cos((chord.source.endAngle-chord.source.startAngle)/2+chord.source.startAngle-Math.PI/2)}
                y1={innerRadius*Math.sin((chord.source.endAngle-chord.source.startAngle)/2+chord.source.startAngle-Math.PI/2)}
                x2={innerRadius*Math.cos((chord.target.endAngle-chord.target.startAngle)/2+chord.target.startAngle-Math.PI/2)}
                y2={innerRadius*Math.sin((chord.target.endAngle-chord.target.startAngle)/2+chord.target.startAngle-Math.PI/2)}>
                <stop offset="0%" stop-color={colors[chord.source.index]}/>
                <stop offset="100%" stop-color={colors[chord.target.index]}/>
            </linearGradient>
        </defs>
        <!--Inner chords-->
        <path
            fill={"url(#"+getGradID(chord)+")"}
            class="ribbon"
            d={ribbonGen(chord)}
            opacity=".8"/>
        {/each}
    </g>
</svg>

<style>
    svg {
        background: None;
    }
    .ribbon {
        opacity: .9;
    }
    .ribbon:hover {
        opacity: 1;
        stroke: black;
        stroke-width: 1;
    }
</style>