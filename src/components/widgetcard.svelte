<script>
  export let widget = {
                widgetid: "",
				name : "",
				emoji: "",
                days: "",
                color: "",
                keywords:[],
    }
  export let deleteWidget;
  export let editWidget;
  export let theme;

var cardlabel = widget.name;
var bgcolor = "black";
if (cardlabel.length >10){ cardlabel = cardlabel.slice(0, 10)+'...';}
if(theme=="g10"){
		bgcolor = "black"
	}
	else {bgcolor = "white"}



</script>

  <svg viewBox="0 0 175.57 131.05" version="1.0">
    <g id="layer1" transform="translate(-351.76 -479.77)">
      <path style="fill:{widget.color};stroke:{bgcolor}" stroke-linejoin="round" d="m443.64 482.73c29.86 0.06 58.93 2.26 68.18 7.08 19.25 10.03 14.04 72.17 0 79.49-2.71 1.41-9.08 2.58-17.74 3.49 0.39 6.2-0.26 15.09-5.75 21.79-12.55 15.32-37.15 13.19-37.15 13.19s22.59-7.92 28.54-19.73c2.28-4.51 2.12-9.6 1.06-14.16-39.47 2.53-102.24 1.29-113.5-4.58-16.15-8.42-17.34-70.45 0-79.49 8.76-4.57 43.05-7.15 76.36-7.08z" stroke="#000" stroke-width="5.9085" fill="#789cae"/>
    </g>
    <text x="50%" y="70" dominant-baseline="middle" text-anchor="middle" class="label" on:click|preventDefault|stopPropagation={editWidget}>{widget.emoji}-{cardlabel}</text>
    <text x="43%" y="35" class="trash" on:click|preventDefault|stopPropagation={deleteWidget}>🗑️</text>
  </svg>  

  <style> 
  
  :root {
    --c: goldenrod;
}

.label {
      font: normal 25px sans-serif;
      cursor: pointer;
    }
.trash {
      font: bold 28px sans-serif;
      cursor: pointer;
    }
    svg {
  height: 35%;
  width: 35%;
}
path {
  fill: var(--c);
  fill-opacity: 0.7;
  stroke: var(--c);
  stroke-width: 2;
  stroke-linejoin: round;
}
path:hover {
  fill: var(--c);
  fill-opacity: 0.7;
  stroke: var(--c);
}
</style>