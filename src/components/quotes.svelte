<script>
  import { onMount } from "svelte";
  export let actualquote;
  export let plugin;

  let widthrefreshed = false
  let textsize = "50%"
  let textcolor = "#50B0EF"

  function init(){
    if (actualquote.text.length > 119){
        textsize = "49%";
    }
    if (plugin.prefs.theme == "light"){
      textcolor = "black"}
    else if (plugin.prefs.theme == "dark") {textcolor = "white"}
    else {plugin.prefs.theme == "#50B0EF"}
    widthrefreshed = true
  }

  function openEditor(){
    let note2nomie = "REFLECTIONS ON FOLLOWING QUOTE FROM "+actualquote.author+":\n";
    note2nomie = note2nomie + actualquote.text+"\n";
    note2nomie = note2nomie + "=> "+"\n";
    plugin.openNoteEditor(note2nomie);
  }

  onMount(
  ()=>{
    init();
  }
)

</script>

<style>
.quote {
        position: absolute;
        width: 100%;
        left:0%;
        height:80%;
        padding-left:0.7%;
        padding-right:0.7%;
        margin-top:-2%;
        vertical-align: middle;
        font-style:italic;
        color:#50B0EF;
    }
    .author{
        position: absolute;
        width: 100%;
        top:80%;
        text-align: right;
        font-weight: bold;
        color:#50B0EF;
    }
</style>
{#if widthrefreshed}
<div class="quote">
  <span on:click={()=>{openEditor()}} style="cursor:pointer"><p style="font-size:{textsize};color:{textcolor}">"{actualquote.text}"</p></span>
  </div>
  <div class="author">
  <span><p style="font-size:55%;color:{textcolor}">-{actualquote.author}</p></span>
  </div>
{/if}

