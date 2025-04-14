<script>
  import { onMount } from 'svelte';
  import "carbon-components-svelte/css/all.css";
  import LibLoader from './components/LibLoadder.svelte';
  import BreathingCard from './components/BreathingCard.svelte';
  import EditBreathingCard from './components/EditBreathingCard.svelte';
  import { saveToLS, readFromLS } from './storage/storage';
  import nid from "./modules/nid/nid";
  import AddCard from './components/AddCard.svelte';
  import BreathingAnimation from './components/BreathingAnimationModal.svelte';
  import GeneralSettingsModal from './components/GeneralSettingsModal.svelte';
  import InfoModal from './components/InfoModal.svelte';
  import {
    Header,
    HeaderUtilities,
    HeaderGlobalAction,
    SkipToContent,
    Content,
    Grid,
    Row,
    Column,
    Theme,
    Tile,
  } from "carbon-components-svelte";
  import SettingsAdjust from "carbon-icons-svelte/lib/SettingsAdjust.svelte";
  import Sun from "carbon-icons-svelte/lib/Sun.svelte";
  import Information from "carbon-icons-svelte/lib/Information.svelte";

  let isNomie = false;
  let isSideNavOpen = false;
  let isOpen1 = false;
  let isOpen2 = false;
  let theme = "g10";
  let plugin;
  var parent = "";

  
  // Load Plugin js
  let PlugiAapiUrl = "https://cdn.jsdelivr.net/gh/open-nomie/plugins/bin/v1/nomie-plugin.js";
  async function onLoaded() {
    plugin = await new NomiePlugin({
        name: "Nomie Breathe",
        emoji: "🫁",
        description: "Breathing Exercises Plugin",
        uses: ["createNote", "getLocation", "selectTrackables","getTrackable"],
        version: "0.21",
        addToCaptureMenu: true,
        addToMoreMenu: true,
        addToWidgets: false,
      }); 
      
      setTimeout(async() => {
        await plugin.storage.init();
      breathings = plugin.storage.getItem('breathings') || [];
      config = plugin.storage.getItem('configuration') || {trackers:["none","none"],trackeroverrule:true,logentry:"Just finished <breathing> exercise with total of <repeats> repeats taking <minutes>."};amountofcards = breathings.length;},500);

      setTimeout(loadInitParams,500);
      setTimeout(() => {if(plugin.prefs != undefined) {isNomie = true}},500)
  }


  // Load init params
  function loadInitParams() {
    parent = getParentUrl();
    if (plugin.prefs.theme == "light") {
      theme = "g10"}
    else if (plugin.prefs.theme == "dark") {
      theme = "g90"}  
    else {theme = "g10"}
  }

  // change theme
  function toggleTheme(){
    if (theme == "white"){
      theme = "g10"}
    else if (theme == "g10"){
      theme = "g80"}
    else if (theme == "g80"){
      theme = "g90"}
    else if (theme == "g90"){
      theme = "g100"}
    else {
      theme = "white"}
 }

 // Get parent
 function getParentUrl() {
    var isInIframe = (parent !== window),
        parentUrl = null;

    var parentfound = null;
    
    if (isInIframe) {
        parentUrl = document.referrer;
    }

    if (parentUrl.includes("nomie") ) {
      parentfound = "Nomie"
    }
    else {parentfound = "Smarter4Ever"}

    return parentfound;
}

 // below is plugin specific code
let id;
let name = "";
let description = "";
let exercise = [];
let trackers = [];
let color = "";
let breathings = [];
let config = {};
let amountofcards = 0;

//let breathings = JSON.parse(readFromLS('breathings')) || [];
//let config = JSON.parse(readFromLS('configuration')) || {trackers:["none","none"],trackeroverrule:true,logentry:"Just finished <breathing> breathing exercise with total of <repeats> repeats taking <minutes> minutes"};
let isEditMode = false;
let isAddMode = false;
let isBreathingMode = false;
let isGeneralSettingsMode = false;
let isInfoMode = false;



// Method to add a breathing    
const addbreathing = () => {
		
		if (isEditMode) {
			editbreathing(id, name, description, exercise,trackers, color);
			
		} else {
			const breathing = {
				id: nid(),
				name : name,
				description: description,
        exercise: exercise,
        trackers:trackers,
				color: color
			};
			breathings = breathings.concat(breathing);
			resetAndSave(breathings);
		}
}

const newbreathing = () => {
    name = "";
		description= "";
    exercise= [5,5,5,5,5];
    trackers=["None","None"];
		color= "#DAAFE9";
    isAddMode = true;
}


// Method to delete a breathing	
const deletebreathing = id => {
		console.log('breathing to delete', id);
		//find breathing by name
		let index = breathings.findIndex(breathing => breathing.id === id);
		//remove breathing
		breathings.splice(index, 1);
		breathings = [...breathings];
		console.log('breathings after delete', JSON.stringify(breathings));
		resetAndSave(breathings);
};

// Method to edit a breathing
const editbreathing = (id, newName, newDescription, newExercise, newTrackers, newColor) => {
		console.log('breathing to edit', name);
		//find breathing by name
		let index = breathings.findIndex(breathing => breathing.id === id);
		//edit breathing
		breathings[index].name = newName;
		breathings[index].description = newDescription;
    breathings[index].exercise = newExercise;
    breathings[index].trackers = newTrackers;
    breathings[index].color = newColor;
		breathings = [...breathings];
		console.log('breathings after edit', breathings);
		resetAndSave(breathings);
};

// Set the edit mode
const editMode = (breathingId) => {
		console.log('breathing to edit', name);
		//find breathing by name
		let breathing = breathings.find(breathing => breathing.id === breathingId);
		id = breathing.id;
		name = breathing.name;
		description = breathing.description;
    exercise = breathing.exercise;
    trackers = breathing.trackers;
    color = breathing.color;
		isEditMode = true;
}

// Method to reset the breathing form
const reset = () => {
		id = '';
		name = '';
		color = '';
		description = '';
    exercise = [5,5,5,5,5]
    trackers = ["None","None"];
		isEditMode = false;
}

// Method to reset and save
const resetAndSave = breathings => {
		reset();
		//saveToLS('breathings', breathings);
    plugin.storage.setItem('breathings', breathings);
    amountofcards = breathings.length;
}

// Method to save from Template
const saveTemplate = (event) => {
  const breathing = {
				id: nid(),
				name : event.detail.template.name,
				description: event.detail.template.description,
        exercise: event.detail.template.exercise,
        trackers:event.detail.template.trackers,
				color: event.detail.template.color
			};
			breathings = breathings.concat(breathing);
			resetAndSave(breathings);

}

// Method to reset via settings modal
const resetAll = async (event) => {
  var res = {};
  res = await plugin.confirm('Resetting to default?', 'All your created exercises will be deleted');
  //res.value = confirm("Are you sure to execute this action?");
    if (res.value) {
      breathings =[];
      resetAndSave(breathings);
      let configuration = {trackers:["none","none"],trackeroverrule:true,logentry:"Just finished <breathing> exercise with total of <repeats> repeats taking <minutes>."};
      config = configuration;
      plugin.storage.setItem('configuration', config);  
    } 
}

const breathingMode = (breathingId) => {
		//find breathing by name
		let breathing = breathings.find(breathing => breathing.id === breathingId);
		id = breathing.id;
		name = breathing.name;
		description = breathing.description;
    exercise = breathing.exercise;
    trackers = breathing.trackers;
    color = breathing.color;
		isBreathingMode = true;
}
const showInfo = (breathingId) => {
  //find breathing by name
  let breathing = breathings.find(breathing => breathing.id === breathingId);
  plugin.alert(breathing.name, breathing.description);
}

const showSettings = () => {
  isGeneralSettingsMode = true;
}

const showInformation = () => {
  isInfoMode = true;
}

const saveSettings = () => {
  //saveToLS('configuration', config);
  plugin.storage.setItem('configuration', config);
  isGeneralSettingsMode=false;
}

const logBreathing = (event) => {
  let note = event.detail;
  plugin.createNote(note);
}

onMount(function() {
        //elmToFocus.focus();
});
</script>

<LibLoader url={PlugiAapiUrl}
on:loaded="{onLoaded}" />

<Theme bind:theme />
{#if isNomie}
<Header company={parent} platformName="Breathe Plugin" bind:isSideNavOpen>
  <svelte:fragment slot="skip-to-content">
    <SkipToContent />
  </svelte:fragment>
  
  <HeaderUtilities>
    <HeaderGlobalAction aria-label="Settings" icon={SettingsAdjust} on:click={showSettings}/>
    <HeaderGlobalAction aria-label="Theme" icon={Sun} on:click={toggleTheme}/>
    <HeaderGlobalAction aria-label="Theme" icon={Information} on:click={showInformation}/>
  </HeaderUtilities>
</Header>

<Content>
  <Grid>
    <Row>
      <Column>
        <h1 style="text-align:center">🫁</h1>
        <h2 style="text-align:center">Breathe Plugin</h2>
        <h5 style="text-align:center">Choose your breathing exercise</h5>
        <hr><br>
      </Column>
    </Row>
    <Row>
      <Column>
  <div class="container">    
		<div>
			<div class="breathing-list">
				{#if breathings.length === 0}
        <Tile>
          <p class="no-breathing">
						No Breathing Exercises? Oh dear, please add one to start practicing. 
          </p>
          
        </Tile>
				{:else}
					{#each breathings as breathing}
						<BreathingCard
							breathing={breathing}
							deletebreathing={() => deletebreathing(breathing.id)} 
							editMode = {() => editMode(breathing.id)} on:click={()=> console.log(breathing)}
              breathingMode = {() => breathingMode(breathing.id)}
              on:showinfo={() => showInfo(breathing.id)} />
					{/each}
        {/if}
        <AddCard bind:amountofcards={amountofcards} on:addnew={newbreathing} on:addbytemplate={saveTemplate}/>

			</div>
		</div>
	</div>
    </Column>
    </Row>
  </Grid>
</Content>
{#if isEditMode || isAddMode}
<EditBreathingCard parent={parent} bind:theme={theme} on:addnew={addbreathing} on:savechanges={addbreathing} bind:name bind:description bind:color bind:exercise bind:trackers bind:isEditMode={isEditMode} bind:isAddMode={isAddMode}></EditBreathingCard>
{/if}

{#if isBreathingMode}
<BreathingAnimation on:exit={()=>{isBreathingMode=false}} on:logbreathing={logBreathing} parent={parent} bind:plugin bind:config breathing={{"name":name,"description":description,"color":color,"exercise":exercise,"trackers":trackers}}></BreathingAnimation>
{/if}

{#if isGeneralSettingsMode}
<GeneralSettingsModal parent={parent} bind:theme={theme} on:resetall={resetAll} on:savetemplate={saveTemplate} bind:config bind:plugin on:savesettings={saveSettings} on:exitsettings={()=>{isGeneralSettingsMode=false}}></GeneralSettingsModal>
{/if}

{#if isInfoMode}
<InfoModal parent={parent} on:exitsettings={()=>{isInfoMode=false}}></InfoModal>
{/if}

{:else}
        <h1 style="text-align:center">🫁</h1>
        <h2 style="text-align:center">Breathe Plugin</h2>
        <h5 style="text-align:center">This is a plugin for {parent}</h5>
{/if}



<style>
.container {
		display: flex;
		justify-content: space-around;
		margin: 1rem auto auto auto;
	}
	@media screen and (max-width: 720px) {
		.container {
			flex-direction: column;
		}
	}
	
	.breathing-list {
		display: flex;
		flex-direction: row;
		flex-wrap: wrap;
		align-content: center;
		justify-content: center;
		align-items: center;
	}
	.no-breathing {
		padding: 1em;
    	border: 1px solid;
    	border-radius: 4px;
	}

  h2 {
        margin: 0;
        padding: 0;
		font-size: 2.5em;
		font-weight: 400;
	}
  

</style>