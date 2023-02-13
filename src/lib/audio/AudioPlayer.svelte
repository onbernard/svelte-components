<script>
    export let src;
    export let title = "title placeholder"
    export let preload = "auto";
    const darkGrey = "#21242C";
    const mediumGrey = "#2C313D";
    const maroon = "#E64553";
    const lightGrey = "#CDD6F4";
    export let progressBarColor = maroon;
    export let borderColor = darkGrey;
    export let bgColor = lightGrey;
    export let textColor = mediumGrey;

    let audio;
    let currentTime = 0;
    let duration;
    let paused = true;

    let totalWidth;
    let progressWidth = "50%";
    $: {
        progressWidth = `${currentTime/duration*100}%`;
    }

    const getTimeCodeFromNum = (num) => {
        let seconds = parseInt(num);
        let minutes = parseInt(seconds / 60);
        seconds -= minutes * 60;
        const hours = parseInt(minutes / 60);
        minutes -= hours * 60;
        if (hours === 0) return `${minutes}:${String(seconds % 60).padStart(2, 0)}`;
        return `${String(hours).padStart(2, 0)}:${minutes}:${String(seconds % 60).padStart(2, 0)}`;
    }
    const clickedBar = (e) => {
        const timeToSeek = e.offsetX / parseInt(totalWidth) * duration;
        audio.currentTime = timeToSeek;
    }
</script>

<audio controls {src} {preload} bind:currentTime bind:paused bind:duration bind:this={audio}>
</audio>

<button class="audio-ctnr" style="--bg-color:{bgColor}; --border-color:{borderColor}" bind:clientWidth={totalWidth} on:click|self={clickedBar}>
    <button class="progress-bar" style="--progress-color:{progressBarColor}; --progress-width:{progressWidth}" on:click|self={clickedBar}></button>
    
    <div class="timestamp">{getTimeCodeFromNum(currentTime)}</div>
    <button class="play-btn" class:played={!paused} class:paused={paused} style="--btn-color:{textColor}" on:click|self={()=>{paused=!paused}}></button>
    <!-- TODO : crop text -->
    <div class="title" style="--title-color: {textColor}">
        {title}
    </div>
</button>

<style>
    audio {
        display: none;
    }
    .audio-ctnr {
        display: flex;
        flex-direction: row;
        background-color: var(--bg-color);
        position: relative;
        height: 10rem;
        align-items: center;
        justify-content: space-around;
        border-radius: 15px;
        border-left: 15px solid var(--border-color);
        border-right: 15px solid var(--border-color);
        z-index: 0;
    }
    button {
        background: none;
        color: inherit;
        border: none;
        padding: 0;
        font: inherit;
        cursor: pointer;
        outline: inherit;
        z-index: 2;
    }
    .timestamp {
        width: 15%;
        font-family:Arial, Helvetica, sans-serif;
        font-size: x-large;
        font-weight: 600;
        color: var(--title-color);
        z-index: 2;
    }
    .play-btn {
        width: 15%;
        padding: 20px;
        height: 100px;
        background-color: var(--btn-color);
        mask-repeat: no-repeat;
        mask-position: center;
    }
    .played {
        mask-image: url(pause-solid.svg);
    }
    .paused {
        mask-image: url(play-solid.svg);
    }
    .title {
        width: 70%;
        overflow-wrap: break-word;
        font-family:Arial, Helvetica, sans-serif;
        font-size: x-large;
        font-weight: 600;
        color: var(--title-color);
        z-index: 2;
    }
    .progress-bar {
        position: absolute;
        top: 0;
        left: 0;
        background-color: var(--progress-color);
        height: 100%;
        width: var(--progress-width);
        z-index: 1;
    }
</style>