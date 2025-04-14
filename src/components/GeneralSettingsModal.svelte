<script>
    import { createEventDispatcher, onMount } from 'svelte'; 
    import nid from "../modules/nid/nid";
    import {
      Button,
      ComposedModal,
      ModalHeader,
      ModalBody,
      ModalFooter,
      Checkbox,
      TextInput,
      MultiSelect,
    } from "carbon-components-svelte";
    import List from "carbon-icons-svelte/lib/TextAlignJustify.svelte";
    import CheckboxIndeterminate from "carbon-icons-svelte/lib/CheckboxIndeterminate.svelte";
    import {breathingTemplates} from "../breathingTemplates";
   
    export let plugin;
    export let config;
    export let theme;
    export let parent;
    let open=true;
    
    let trackeroverrule = true;
    let trackers = ["none","none"];
    let logentry = "";
    let trackersDisplay =["∅ None","∅ None"];
    let selectedTemplates =[];
    let TemplateArray = [];
    let exercisetemplates = [];
    const dispatch = createEventDispatcher();

    // Set background
	let themecolor = "#E9E9E9";
	let themefont = "#161616"
	if(theme=="g10"){
		themecolor = "grey";
		themefont = "#161616"
	}
	else {themecolor = "lightgrey";
	themefont = "#F4F4F4"}

    $: if(config){
      initConfig();
    }
   
    const addTracker = async (id) => {
      const justOneTrackable = await plugin.selectTrackables('tracker',false);
      if(justOneTrackable) {
        trackers[id]=justOneTrackable[0].tracker.tag;
        trackersDisplay[id]=justOneTrackable[0].tracker.emoji+" "+justOneTrackable[0].tracker.label;
      }
      
    }

    const removeTracker = async (id) => {
      trackers[id] = "none";
      trackersDisplay[id] = "∅ None";

      
    }

    const reset = () => {
      // reset hobboes
      dispatch("resetall");
      // reset config
      // save all
      // re-inityse data
    }

    
    function exitSettings(){
        dispatch("exitsettings");
        open = false;
    }

    function saveSettings(){
      // code to save settings
        config.trackers = trackers;
        config.trackeroverrule = trackeroverrule;
        config.logentry = logentry;
        dispatch("savesettings");
        open = false;
    }

    function initTemplates(){
      breathingTemplates.forEach((template) => {
    TemplateArray.push({id:nid(),name:template.name,description:template.description,exercise:template.exercise,trackers:template.trackers,color:template.color})
    });
    let i = 0;
    TemplateArray.forEach((item) => {
      exercisetemplates.push({ id: i.toString(), text: item.name, ref:item.id })
      i=i+1;
    });
    }

    async function initConfig(){
      trackeroverrule = config.trackeroverrule;
      trackers = config.trackers;
      logentry = config.logentry;
      //trackers
      let trackable1;
      let trackable2;
      if (trackers[0] != "none") {
      trackable1 = await plugin.getTrackable('#'+trackers[0]);}
      else { trackable1 = {trackable:{tracker:{label:"None",emoji:"∅"}}}};
      if (trackers[1] != "none") {
      trackable2 = await plugin.getTrackable('#'+trackers[1]);}
      else { trackable2 = {trackable:{tracker:{label:"None",emoji:"∅"}}}};
      trackersDisplay =[trackable1.trackable.tracker.emoji+" "+trackable1.trackable.tracker.label,trackable2.trackable.tracker.emoji+" "+trackable2.trackable.tracker.label];
      }

    onMount (() =>{
      initConfig();
      initTemplates();

    })
</script>


<ComposedModal bind:open on:close={() => { exitSettings();}} on:submit={() => { saveSettings();}} >
    <ModalHeader label="{parent} Breathe" title="General Settings" />
    <ModalBody>
      <h1 style="text-align:center">🫁</h1>
      <h2 style="text-align:center">{parent} Breathe</h2>
      <h5 style="text-align:center">General Settings</h5>
      <hr><br>
      <h4>Log Settings:</h4>
      <Checkbox disabled labelText="Overrule individual Tracker Alignment" bind:trackeroverrule />
      <h3>Exercise Repeats:</h3>
      <tr>
        <td style="vertical-align:middle; text-align:center;width:20px"><span on:click={()=>{removeTracker(0)}}><CheckboxIndeterminate size={32} style="color:{themecolor};cursor: pointer;display:table-cell;vertical-align:middle;margin-top:0px"></CheckboxIndeterminate></span></td>
        <td style="vertical-align:middle; text-align:center;width:20px"><span on:click={()=>{addTracker(0)}}><List size={32} style="color:{themecolor};cursor: pointer;display:table-cell;vertical-align:middle;margin-top:0px"></List></span></td>
        <td style="vertical-align:middle ;text-align:left;width:250px"><p style="display:inline;font-weight:300;text-align:left;vertical-align:middle;">{trackersDisplay[0]}</p></td>
     </tr>
     <h3>Exercise Total Time:</h3>
      <tr>
        <td style="vertical-align:middle; text-align:center;width:20px"><span on:click={()=>{removeTracker(1)}}><CheckboxIndeterminate size={32} style="color:{themecolor};cursor: pointer;display:table-cell;vertical-align:middle;margin-top:0px"></CheckboxIndeterminate></span></td>
        <td style="vertical-align:middle; text-align:center;width:20px"><span on:click={()=>{addTracker(1)}}><List size={32} style="color:{themecolor};cursor: pointer;display:table-cell;vertical-align:middle;margin-top:0px;"></List></span></td>
        <td style="vertical-align:middle ;text-align:left;width:250px"><p style="display:inline;font-weight:300;text-align:left;vertical-align:middle;">{trackersDisplay[1]}</p></td>
     </tr>
     <h3>Additional Log Entry Input:</h3>
     <TextInput bind:value={logentry} placeholder="Enter additional Log input..."  helperText="You can include a reference to the related Breathing Exercise. For instance: Results of my <breathing> are a total of <repeats> repeats taking <minutes>."/>
      <hr>
      <br>
      <h4>Reset to initial setup:</h4>
      <h6>This will remove all exercises and reset settings</h6>
      <Button on:click={reset}>Reset</Button>
      
    
    </ModalBody>
    <ModalFooter
      primaryButtonText="Update & Exit"
      primaryButtonDisabled={false} 
      secondaryButtons={[{ text: "Cancel" }]}
      on:click:button--secondary={({ detail }) => {
        if (detail.text === "Cancel") exitSettings(); 
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

  h6 {
        margin: 0;
        padding: 0;
		font-size: 0.8em;
		font-weight: 300;
	}

  p {
        height: 45px;
        line-height: 45px;
        text-align: center;
      }
  h3 {
    font-size:1.2em;
    font-weight: 300;
  }

  </style>
