---
layout: page
type: page
title: 二维码(QRCode)编解码
---
<div class="input-group mb-3" title="Popover title"
            data-container="body" data-toggle="popover" data-placement="bottom"
            data-content="底部的 Popover 中的一些内容">
  <input id="text" type="text" class="form-control" placeholder="" aria-label="Recipient" aria-describedby="basic-addon2">
  <div class="input-group-append">
    <span class="input-group-text" id="basic-addon2">回车自动刷新</span>
  </div>
</div>

<span class="badge badge-secondary" style="display:block" id="tips" >请右键点击二维码 -> 保存图片</span>
<div id="qrcode" style="width:300px; height:300px; margin-top:15px;"></div>

<!-- <button type="button" class="btn btn-primary glyphicon glyphicon-download" id='download'>Download</button> -->

<script type="text/javascript">

$(document).ready(function(){
    var qrcode = null;
function makeCode () {		
	let elText = $("#text").val();
	
	if (!elText) {
        // alert("Input a text");
        // $("[data-toggle='popover']").popover();
        $("#text").focus();
        $("#tips").hide()
		return;
    }
    
    $("#tips").show()
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
