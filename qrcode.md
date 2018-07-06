---
layout: page
title: qrcode
---
<input id="text" type="text" value="http://blog.ilibrary.me" style="width:80%" class="form-control" /><br />
<div id="qrcode" style="width:300px; height:300px; margin-top:15px;"></div>

<script type="text/javascript">

$(document).ready(function(){
function makeCode () {		
	let elText = $("#text").val();
	
	if (!elText) {
		alert("Input a text");
		$("#text").focus();
		return;
	}
    // $("canvas").remove()
        // Clear all canvases found in the given div, including all child canvases and sub child canvases.
$('#qrcode canvas').each(function(idx, item) {
var context = item.getContext("2d");
context.clearRect(0, 0, item.width, item.height);
context.beginPath();        
});
$('#qrcode').empty()

setTimeout(function(){ // if no delay, there will be empty
    $("#qrcode").qrcode({width: 64,height: 64,text: elText})}
    , 500)

}

makeCode();

$("#text").
	on("blur", function () {
		makeCode();
	}).
	on("keydown", function (e) {
		if (e.keyCode == 13) {
			makeCode();
		}
    });
})
</script>