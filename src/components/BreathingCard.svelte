<script>
    import { createEventDispatcher } from 'svelte'; 
    import TrashCan from "carbon-icons-svelte/lib/TrashCan.svelte";
    import Edit from "carbon-icons-svelte/lib/Edit.svelte";
    import Information from "carbon-icons-svelte/lib/Information.svelte";
    export let breathing = {
        id: '', 
        name: '', 
        description: '',
        color: '',
        exercise: [],
        trackers: []
    }
    export let deletebreathing;
    export let editMode;
    export let breathingMode;
    
    const dispatch = createEventDispatcher();
    let inhalestyle = "";
    let holdstyle = "";
    let exhalestyle = "";
    let hold2style = "";
    let inhale = "";
    let hold = "";
    let exhale = "";
    let hold2= "";
    let perc1;
    let perc2;
    let perc3;
    let perc4;
    

  $: if (breathing.exercise) {
    inhale = breathing.exercise[0];
    hold = breathing.exercise[1];
    exhale = breathing.exercise[2];
    hold2 = breathing.exercise[3];
    perc1 = Math.floor((inhale/(inhale+hold+exhale+hold2))*100,0);
    perc2 = Math.floor((hold/(inhale+hold+exhale+hold2))*100,0);
    perc3 = Math.floor((exhale/(inhale+hold+exhale+hold2))*100,0);
    perc4 = Math.floor((hold2/(inhale+hold+exhale+hold2))*100,0);
    inhalestyle = "width:"+perc1+"%; ";
    holdstyle = "width:"+perc2+"%; ";
    exhalestyle = "width:"+perc3+"%; ";
    hold2style = "width:"+perc4+"%; ";


    // find first value
    if (perc1 > 0) {
        inhalestyle = inhalestyle+"border-top-left-radius: 10px; border-bottom-left-radius: 10px;";
    }
    else if (perc2 > 0) {
        holdstyle = holdstyle+"border-top-left-radius: 10px; border-bottom-left-radius: 10px;";
    }
    else if (perc3 > 0) {
        exhalestyle = exhalestyle+"border-top-left-radius: 10px; border-bottom-left-radius: 10px;";
    }
    else if (perc4 > 0) {
        hold2style = hold2style+"border-top-left-radius: 10px; border-bottom-left-radius: 10px;";
    }
    // find last value
    if (perc4 > 0) {
        hold2style = hold2style+"border-top-right-radius: 10px; border-bottom-right-radius: 10px;";
    }
    else if (perc3 > 0) {
        exhalestyle = exhalestyle+"border-top-right-radius: 10px; border-bottom-right-radius: 10px;";
    }
    else if (perc2 > 0) {
        holdstyle = holdstyle+"border-top-right-radius: 10px; border-bottom-right-radius: 10px;";
    }
    else if (perc1 > 0) {
        inhalestyle = inhalestyle+"border-top-right-radius: 10px; border-bottom-right-radius: 10px;";
    }
  }

  function showInfo(){
    dispatch("showinfo");
  }

    
        
</script>

<div class="breathing" style="background-color: {breathing.color}; color:black;" on:click={breathingMode}>
    <div class="row">
        <div class="column"><div class="info">
            <span on:click|preventDefault|stopPropagation={showInfo}><Information size={20} /></span>
        </div></div>
        <div class="column"><div class="actions">
            <span on:click|preventDefault|stopPropagation={editMode}><Edit size={20} /></span>
            <span on:click|preventDefault|stopPropagation={deletebreathing}><TrashCan size={20} /></span>
    </div></div>
      </div> 
    
    
    <span hidden>{breathing.id}</span>
    <div class="title">
     <h3 style="text-align:center"><b>{breathing.name}</b></h3>
    </div> 
    <div class="description">
        <p hidden>{breathing.description}</p>
    </div>
    <div>
        <span>
            <div class="bar">
                <div class="inhale" style={inhalestyle}>
                    {#if inhale > 0}
                    <h5>{inhale}s</h5>
                    {/if}
                </div>
                <div class="hold" style={holdstyle}>
                    {#if hold > 0}
                    <h5>{hold}s</h5>
                    {/if}
                </div>
                <div class="exhale" style={exhalestyle}>
                    {#if exhale > 0}
                    <h5>{exhale}s</h5>
                    {/if}
                </div>
                <div class="hold2" style={hold2style}>
                    {#if hold2 > 0}
                    <h5>{hold2}s</h5>
                    {/if}
                </div>
            </div>
       </span>
      
    </div>
</div>

<style>
    h3 {
        margin: 0;
        padding: 0;
		font-size: 1.5em;
		font-weight: 400;
        
	}

    h5 {
        margin: 0;
        padding: 0;
		font-size: 0.8em;
		font-weight: 400;
        
	}

    .breathing {
        padding: 0.3rem;
        margin: 0.7rem;
        border: 0px solid #ececec;
        border-radius: .5rem;
        width: 15rem;
        display: flex;
        flex-direction: column;
        justify-content: center;
        cursor: pointer;
    }

    .actions {
        justify-content: flex-end;
        display: flex;
    }

    .actions span {
        padding: 0 0 0 .5rem;
        cursor: pointer;
    }

    .info {
        justify-content: flex-start;
        display: flex;
    }
    .info span {
        padding: 0 0 0 .5rem;
        cursor: pointer;
    }
   

    .description {
        height:0.9em;
        text-align:center;
    }

    .title{
        height:2em;
        text-align:center;
    }
    
    .bar {
        width: 100%;
        height: 13px;
        border-radius: 10px;
        background-color: transparent;
        color: white;
        text-align: center;
        margin-bottom:-90px;
    }

    .inhale {
        width: 50%;
        height: 100%;
        background-color: green;
        float: left;
        border-top-left-radius: 10px;
        border-bottom-left-radius: 10px;
    }

    .hold {
        width: 25%;
        height: 100%;
        background-color: #3366ff;
        color:white;
        float: left;
    }

    .exhale {
        width: 10%;
        height: 100%;
        background-color: rgb(2, 94, 2);
        float: left;
    }

    .hold2 {
        width: 15%;
        height: 100%;
        background-color: #3366ff;
        color:white;
        float: left;
        border-top-right-radius: 10px;
        border-bottom-right-radius: 10px;
    }

    .column {
  float: left;
  width: 50%;
}

/* Clear floats after the columns */
.row:after {
  content: "";
  display: table;
  clear: both;
}
</style>