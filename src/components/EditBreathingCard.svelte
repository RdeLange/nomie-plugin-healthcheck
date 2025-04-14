<script>
    import { createEventDispatcher } from 'svelte'; 
    import ColorPicker from './ColorPicker.svelte';
    import {
      Button,
      ComposedModal,
      ModalHeader,
      ModalBody,
      ModalFooter,
      Slider,
      TextArea
    } from "carbon-components-svelte";
  
    export let name = "Dummy";
    export let description = "Dummy and dummy";
    export let isEditMode = true;
    export let isAddMode = true;
    export let color = "#DAAFE9";
    export let exercise = [5,5,5,5,5];
    export let theme;
    export let parent;
    const dispatch = createEventDispatcher();
    let open = true;
    let label = "";
    let title = "";
    let dispatchcommand = "";

    if (isEditMode) {
        label = parent+" Breathe"
        title = "Edit Breathing Exercise"
        dispatchcommand = "savechanges";

    }
    else {
        label = parent+" Breathe"
        title = "Add Breathing Exercise"
        dispatchcommand = "addnew";
    }

    function saveBreathing(){
        isAddMode=false; 
        //if (dispatchcommand =! "savechanges"){isEditMode=false;}
        dispatch(dispatchcommand);
        open = false;
    }
  </script>
  
  
  <ComposedModal bind:open on:submit={() => saveBreathing()} on:close={() => {isAddMode=false; isEditMode=false; open = false;}}>
    <ModalHeader label={label} title={title} />
    <ModalBody hasForm>
        <h1 style="text-align:center">🫁</h1>
      <h2 style="text-align:center">{parent} Breathe</h2>
      <h5 style="text-align:center">{title}</h5>
      <hr><br>
        <TextArea rows={1} maxCount={20} labelText="Exercise name" placeholder="Enter breathing exercise name..." bind:value={name}/>
        <TextArea rows={2} maxCount={200} labelText="Exercise description" placeholder="Enter a description..." bind:value={description}/>
        <p>Color:</p>
        <ColorPicker bind:theme={theme} bind:value={color}></ColorPicker>
        <br>
        <Slider
            labelText="Inhale (seconds)"
            min={0}
            max={15}
            maxLabel="15"
            bind:value={exercise[0]}
        />
        <Slider
            labelText="Hold (seconds)"
            min={0}
            max={15}
            maxLabel="15"
            bind:value={exercise[1]}
        />
        <Slider
            labelText="Exhale (seconds)"
            min={0}
            max={15}
            maxLabel="15"
            bind:value={exercise[2]}
        />
        <Slider
            labelText="Hold (seconds)"
            min={0}
            max={15}
            maxLabel="15"
            bind:value={exercise[3]}
        />
        <br>
        <Slider
            labelText="Repeats"
            min={1}
            max={50}
            maxLabel="50"
            bind:value={exercise[4]}
        />     
        
    </ModalBody>
    <ModalFooter
      primaryButtonText="Proceed"
      primaryButtonDisabled={!name || !description}
      secondaryButtons={[{ text: "Cancel" }]}
      on:click:button--secondary={({ detail }) => {
        if (detail.text === "Cancel") isAddMode=false; isEditMode=false; open = false;
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
  
  