<script>
  import { onMount } from "svelte";
  import { quotestemplates } from "./widget-quotes-templates";
  import Quotes from "./quotes.svelte";

  export let Widgets;
  export let WidgetIndex;
  export let plugin;

  var showwidget = false;
  let qtemplates =[];
  let qtheme = "";
  let qquotes = {};
  let qlabel = "";
  let qemoji = "";
  let actualquote = "loading"
  let bgcolor = "white";
  

async function initwidget() {
  if (plugin.prefs.theme == "light" || plugin.prefs.theme == "auto"){
    bgcolor = "white"
  }
  else {bgcolor = "black"}
  //insert code
  let index = WidgetIndex;
  let Widget = Widgets.find((p) => p.widgetid == index);
  if (Widget) {
  qtheme = Widget.config.quotetheme;
  qlabel = Widget.name;
  qemoji = Widget.emoji;}
  else {
    Widget = {"widgetid":"4567", "name":"Placeholder","emoji":"👁️‍🗨️","color":"#C47ADA","config":{"quotetheme":"custom","quotes":[{"text": "Nomie has been a labor of love for me for the past 8 years, and I am deeply grateful for the support and loyalty of the community.","author": "❤️ Brandon"}]}}
    qtheme = Widget.config.quotetheme;
    qlabel = Widget.name;
    qemoji = Widget.emoji;
  }
  if (qtheme == "custom") {
    qquotes.quotes = Widget.config.quotes;
  if (qquotes.quotes.length == 0) {
    qquotes.quotes = [{"text":"No quotes available","author":"none"}]
  }}
  else {
    qquotes = quotestemplates.find((quotes) => quotes.quotesname == qtheme)
  }
  actualquote = await getQuoteOfDay()
  //end insertcode 
  showwidget = true;
}


async function getQuoteOfDay(){
      var intQuoteCount = qquotes.quotes.length; // The number of quotes in your library
      var dtNow = new Date();
      var intTZOffset = dtNow.getTimezoneOffset() * 60000; // automatically adjust for user timezone
      var intNow = dtNow.getTime() - intTZOffset;
      var intDay = Math.floor(intNow / 86400000); // The number of 'local' days since Jan 1, 1970
      var intQuoteToDisplay = intDay % intQuoteCount;
  return qquotes.quotes[intQuoteToDisplay];
  }


onMount(
  ()=>{
    initwidget();
  }
)

</script>

<style>
.extra-outer-wrapper {
		width: 100%;
		margin: 0 auto;
    height: 100%;
    color:#50B0EF;
	}

	.outer-wrapper {
		width: 100%;
		position: relative;
    height: 100%
	}
	
	.inner-wrapper {
		width: 100%;
    height: 100%;
		display: flex;
		position: absolute;
    
	}

	.content {
		flex: 0 0 auto;
		width: 95%;
		height: 100%;
	  align-items: center;
		justify-content: center;
		display: flex;
		text-align: center;
		font-weight: bold;
		font-size: 2rem;
		color: white;
    border-radius:10px;
    background:transparent;
	}

 
</style>

{#if showwidget}
<div class="extra-outer-wrapper" style="background:{bgcolor}">
  <div class="outer-wrapper" style="background:{bgcolor}">
 		<div class="inner-wrapper" style="background:{bgcolor}">
			<div class="content" style="background:{bgcolor}">
        <Quotes
        bind:actualquote={actualquote} plugin={plugin}/>
        </div>
		</div>
	</div>
</div>
 {/if}