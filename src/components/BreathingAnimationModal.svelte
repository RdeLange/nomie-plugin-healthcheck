<script>
    import { createEventDispatcher, onMount } from 'svelte'; 
    import "carbon-components-svelte/css/all.css";
    import {
      Button,
      ComposedModal,
      ModalHeader,
      ModalBody,
      ModalFooter,
      ProgressBar,
      Slider,
      Grid,
      Row,
      Column,
      Content
    } from "carbon-components-svelte";
    import BreathingAnimation from './Breathing-animation.svelte';

   
    export let breathing = {id:"123",name: "Default Breathing",description:"This is a default Breathing Exercise",exercise:[5,5,5,5,2],trackers:["None","None"],color:"#C7DBF5"};
    export let config;
    export let plugin;
    export let parent;
    
    const dispatch = createEventDispatcher();
    let exercisename = breathing.name;
    let phase = "Paused";
    let phaseid = "paused";
    let status = "paused";
    let open = true;
    let inhale = 0;
    let hold1 = 0;
    let exhale = 0;
    let hold2 = 0;
    let repeats = 0;
    let repeatcount=0;
    let repeatstogo =0;
    let breathId;
    let totalTime = 0;
    let running = false;

    inhale = breathing.exercise[0]*1000;
    hold1 = breathing.exercise[1]*1000;
    exhale = breathing.exercise[2]*1000;
    hold2 = breathing.exercise[3]*1000;
    repeats = breathing.exercise[4];
    repeatstogo = repeats;

    $: if(repeats){
      repeatstogo=repeats;
    }

    $: if(status){
      if (status=="running"){
        running = true;
      }
      else {running = false}
    }

    
function startBreathing(){
  totalTime = 1000*(breathing.exercise[0] + breathing.exercise[1] + breathing.exercise[2] + breathing.exercise[3]);
breathId = null;  
repeatcount = 0; 
repeatstogo = repeats-repeatcount; 
phase ="";
phaseid = "";
status="running";
BreathAnimation(); // execute the breathanimation function at the beginning
if (repeats >1){breathId = setInterval(BreathAnimation, totalTime);} // repeat
}

function BreathAnimation(){
  totalTime = 1000*(breathing.exercise[0] + breathing.exercise[1] + breathing.exercise[2] + breathing.exercise[3]);
repeatcount = repeatcount+1;  
repeatstogo = repeats-repeatcount;
  if (repeatcount === (repeats)) {
  clearInterval(breathId);
  setTimeout(()=>{status="paused";phase = "Paused"}, totalTime);
  setTimeout(()=>{logBreathing()}, totalTime+100);}
  
  phase = 'Breathe In';
  phaseid = 'inhale';
  
  setTimeout(function(){
    if(breathing.exercise[1] > 0){
    phase = 'Hold On';
    phaseid = "hold1";}
    
    setTimeout(function(){
      if(breathing.exercise[2] > 0){
      phase = 'Breathe Out';
      phaseid = "exhale";}
      setTimeout(function(){
        if(breathing.exercise[3] > 0){
        phase = 'Hold On';
        phaseid = "hold2";}},breathing.exercise[2]*1000) },breathing.exercise[1]*1000)},breathing.exercise[0]*1000);   

}

function exitBreathing(){
    clearInterval(breathId);
    repeatcount=0;
    phase = "Paused";
    phaseid = "paused";
    status = "paused";
    dispatch("exit");
    open = false;
    }

async function logBreathing(){
  
  
if(config.trackeroverrule){
  if(config.trackers[0] != "none" || config.trackers[1] != "none") {
    var res = {};
    res = await plugin.confirm('Log session to '+parent, 'This will log your session in '+parent+' as you configured via settings');
   // res.value = confirm("Log session");
      if (res.value) {
        let trackinglog ="";
          if (config.trackers[1] != "None") {
            let timertrack = "#"+config.trackers[1]+"("+((totalTime/1000)*repeats).toString()+")";
            trackinglog = timertrack+"\n";
          }
          if (config.trackers[0] != "None") {
          let repeattrack = "#"+config.trackers[0]+"("+(repeats).toString()+")";
          trackinglog = trackinglog + repeattrack;
          }
        let logentry = config.logentry;
        logentry = logentry.replace("<repeats>", repeats.toString());

        var time_s = (totalTime*repeats)/1000;
        var minute = Math.floor(time_s/60);
        var rest_seconds = time_s%60;
        const result = minute + " minutes " + rest_seconds + " seconds"
        logentry = logentry.replace("<minutes>", result);
        logentry = logentry.replace("<breathing>", exercisename);
        const note = parent+" Breathe 🫁:\n"+logentry+"\n\n"+trackinglog;
        dispatch("logbreathing",note);
        }
      }
  }
}

</script>


<ComposedModal size="xs" bind:open on:close={() => { exitBreathing();}} on:submit={() => { startBreathing();}}>
    <ModalHeader label="{parent} Breathe" title={exercisename} />
    <ModalBody>
      
      <h1 style="text-align:center">🫁</h1>
      <h2 style="text-align:center">{parent} Breathe</h2>
      <hr>
             
              
              <Slider
                  disabled={running}
                  hideTextInput
                  fullWidth = false
                  labelText="Repeats"
                  min={1}
                  max={50}
                  maxLabel="50 Repeats"
                  bind:value={repeats}
                /> 
              <BreathingAnimation bind:repeatstogo bind:breathingcolor={breathing.color} bind:phase bind:phaseid bind:inhale bind:hold1 bind:exhale bind:hold2 bind:active={status}></BreathingAnimation>
              
              
    </ModalBody>
    <ModalFooter
      primaryButtonText="Start"
      primaryButtonDisabled={running} 
      secondaryButtons={[{ text: "Cancel" }]}
      on:click:button--secondary={({ detail }) => {
        if (detail.text === "Cancel") exitBreathing(); 
      }}
      on:click:button--primary={({ detail }) => {
        if (detail.text === "Start") startBreathing(); 
      }}
    />
  </ComposedModal>

  <style>
     h2 {
        margin: 0;
        padding: 0;
		font-size: 2.5em;
		font-weight: 400;
	}
  </style>
