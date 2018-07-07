---
layout: page
title: qrcode
---
<input id="text" type="text" value="http://blog.ilibrary.me" style="width:80%" class="form-control" /><br />
<span class="badge badge-secondary">请右键点击二维码 -> 保存图片</span>
<div id="qrcode" style="width:300px; height:300px; margin-top:15px;"></div>

<!-- <button type="button" class="btn btn-primary glyphicon glyphicon-download" id='download'>Download</button> -->

<script type="text/javascript">

$(document).ready(function(){
    var qrcode = null;
function makeCode () {		
	let elText = $("#text").val();
	
	if (!elText) {
		alert("Input a text");
		$("#text").focus();
		return;
	}
    
$('#qrcode').empty()

setTimeout(function(){ // if no delay, there will be empty
    qrcode = $("#qrcode").qrcode({width: 64,height: 64,text: elText})}
    , 500);

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

// $("#download").click(function(){
//     var canvas = qrcode.find('canvas').get(0)//$("#qrcode > canvas").first();
//     var img    = canvas.toDataURL("image/png");
//     window.location = img
// })
}
)
</script>