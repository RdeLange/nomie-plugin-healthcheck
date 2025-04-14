<script>
import {
    Button,
    Content,
      Grid,
      Row,
      Column,
      TextArea,
      Tile,
      Select, SelectItem
  } from "carbon-components-svelte";

  export let quotes;

  let refreshed = true;

  function addQuote(){
    if (quotes.length > 0) {
        if (quotes[(quotes.length-1)].text !="" || quotes[(quotes.length-1)].author !=""){
            quotes.unshift({text:"",author:""})
        }
    }
    else {quotes.push({text:"",author:""})}
    
    refreshed = false;
    setTimeout(()=>{refreshed = true},10)
  }

  function deleteQuote(event){
    quotes.splice(event.detail, 1);
    refreshed = false;
    setTimeout(()=>{refreshed = true},10)
  }
  
</script>

    <Tile id="quotelist" style="max-height:350px;overflow-y:scroll">
     <hr>   
    {#if refreshed}
    <div class="row">
        <div class="column" style="background-color:#9E9287;height:45px;text-align:center;width:100%;cursor: pointer;" on:click={()=>addQuote()}>
          <h4>ADD QUOTE</h4>
          </div>
      </div>
    {#each quotes as quote, i (i)}
    <hr>
    <div class="row">
        <div class="column" style="background-color:#9E9287;height:45px;text-align:center;width:100%">
          <h4>QUOTE & AUTHOR</h4>
          </div>
      </div>
    <div class="row">
        <div class="column" style="background-color:#aaa;width:100%">
            <p>Quote</p>
        <p><TextArea hideLabel rows={2} labelText="Quote" placeholder="Enter Quote..." bind:value={quote.text}/></p>
        </div>
    </div>
    <div class="row">
        <div class="column left" style="background-color:#aaa;">
            <p>Author</p>
          <p><TextArea hideLabel rows={1} labelText="Author" placeholder="Enter Author..." bind:value={quote.author}/></p>
        </div>
        <div class="column right" style="background-color:#aaa;text-align:center">
          <span><h1 style="cursor:pointer" on:click={(i)=>{deleteQuote(i-1)}}>🗑️</h1></span>
        </div>
      </div>
    {/each}
      {/if}
    </Tile> 


 <style>

* {
  box-sizing: border-box;
}

/* Create two unequal columns that floats next to each other */
.column {
  float: left;
  padding: 10px;
}

.left {
  width: 75%;
  height: 85px;
}

.right {
  width: 25%;
  height: 85px;
  line-height: 85px;
}

span {
  display: inline-block;
  vertical-align: middle;
}


/* Clear floats after the columns */
.row:after {
  content: "";
  display: table;
  clear: both;
}
 </style>   