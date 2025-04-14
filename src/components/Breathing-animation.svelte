<script>
    import { onMount } from 'svelte';
    export let phase;
    export let phaseid;
    export let inhale = 4000;
    export let hold1 = 3000;
    export let exhale = 4000;
    export let hold2 = 3000;
    export let active= "paused";
    export let breathingcolor = "black";
    export let repeatstogo = 0;

    let className = "";
    let duration = "";

    let totaltime ;
    let totalduration;
    let perc1;
    let perc2;
    let perc3;
    let conicstyle;
    let isrefreshed = true;

    $: if(inhale || hold1 || exhale || hold2){
        initAnimation();
        isrefreshed=false;
    setTimeout(()=>{isrefreshed = true}, 5);
    }
    function initAnimation(){
        active="paused";
        totaltime = inhale+hold1+exhale+hold2;
        totalduration = (totaltime/1000)+"s";

        perc1 = Math.round((inhale/totaltime)*100,0);
        perc2 = perc1+Math.round((hold1/totaltime)*100,0);
        perc3 = perc2+Math.round((exhale/totaltime)*100,0);
        conicstyle = '#55b7a4 0%, #4ca493 '+perc1+'%, #3366ff '+perc1+'%, #3366ff '+perc2+'%, #336d62 '+perc2+'%,#2a5b52 '+perc3+'%, #3366ff '+perc3+'%,#3366ff 100%';
        className = "animationcontainer start";
    }

$: if(phaseid){
    if(phaseid =="inhale"){
        className = "animationcontainer grow";
        duration = (inhale/1000)+"s";


    }
    else if(phaseid =="exhale"){
        className = "animationcontainer shrink";
        duration = (exhale/1000)+"s";
    }
    else if(phaseid =="hold1"){
        className = "animationcontainer hold1";
        duration = (hold1/1000)+"s";
    }
    else if(phaseid =="hold2"){
        className = "animationcontainer hold2";
        duration = (hold2/1000)+"s";
    }
}    

onMount(() => {
    initAnimation();
});
</script>
<style>
   * {
    box-sizing: border-box;
}


.animationcontainer {
    display: flex;
    align-items: center;
    justify-content: center;
    margin: auto;
    height: 300px;
    width: 300px;
    position: relative;
    transform: scale(0.7);
}

.circle {
    background-color: #010f1c;
    height: 100%;
    width: 100%;
    border-radius: 50%;
    position: absolute;
    top: 0;
    left: 0;
    z-index: -1;
}

.gradient-circle {
    background: conic-gradient(
    #55b7a4 0%,
    #4ca493 29%,
    #fff 29%,
    #fff 50%,
    #336d62 50%,
    #2a5b52 79%,
    #fff 79%,
    #fff 100%
  );
  height: 340px;
  width: 340px;
  z-index: -2;
  border-radius: 50%;
  position: absolute;
  top: -20px;
  left: -20px;
}

.pointer {
    background-color: #3366ff;
    border-radius: 50%;
    height: 20px;
    width: 20px;
    display: block;
}

.pointer-container {
    position: absolute;
    top: -50px;
    left: 135px;
    width: 25px;
    height: 200px;
    animation: rotate 14s linear forwards infinite;
    transform-origin: bottom center;
}

@keyframes rotate {
    from {
        transform: rotate(0deg);
    }

    to {
        transform: rotate(360deg);
    }
}

.animationcontainer.start {
    animation: start 0s linear forwards;
}

@keyframes start {
    from {
        transform: scale(0.7);
    }
    to {
        transform: scale(0.7);
    }
}
.animationcontainer.grow {
    animation: grow 3s linear forwards;
}

@keyframes grow {
    from {
        transform: scale(0.7);
    }
    to {
        transform: scale(0.9);
    }
}

.animationcontainer.hold1 {
    transform: scale(0.9)
}

.animationcontainer.shrink {
    /*animation: shrink 3s linear forwards;*/
    animation-name: shrink;
    animation-duration: 3s;
    animation-timing-function: linear;
    animation-direction: forwards;
}

@keyframes shrink {
    from {
        transform: scale(0.9);
    }
    to {
        transform: scale(0.7);
    }
}
.animationcontainer.hold2 {
    transform: scale(0.7)
}

</style>
{#if isrefreshed}
  <div class = {className} style="animation-duration: {duration};">
  
    <div class="circle" style="background-color:{breathingcolor};color:black"></div>
    <table>
        <tr>
    <h3 style="text-align:center; color:black">{repeatstogo}</h3>
        </tr>
        <tr>
    <h2 style="text-align:center; color:black" id={phase}>{phase} {duration}</h2>
    </tr>
    </table>
    <div class="pointer-container" style="animation-duration: {totalduration};animation-play-state: {active};">
      <div class="pointer"></div>
    </div>
    <div class="gradient-circle" style="background: conic-gradient({conicstyle};"></div>
  </div>
  {/if}