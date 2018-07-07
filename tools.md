---
layout: page
type: page
title: tools
---

<div class="panel">

 <div>
   <form>
     <div class="form-group">
       <label for="source">URL源字符串:</label>
       <textarea class="form-control" rows="5" id="source"></textarea>
     </div>
   
   
        <button type="button" class="btn btn-primary glyphicon glyphicon-arrow-down" id='encode'>URL Encode</button>
        <button type="button" class="btn btn-success glyphicon glyphicon-arrow-up" id='decode'>URL Decode</button>
        <div class="form-group">
           <label for="encoded">URL编码字符串:</label>
           <textarea class="form-control" rows="5" id="encoded"></textarea>
         </div>
   </form>
 </div>
<div class="panel">

<script>
$(document).ready(
   function(){
      $('#encode').click(function(){
            var uri = $("#source").val()// "https://w3schools.com/my test.asp?name=ståle&car=saab";
            var uri_enc = encodeURIComponent(uri); 
            
            $("#encoded").val(uri_enc);
      })
      
      $('#decode').click(function(){
                  var uri = $("#encoded").val()// "https://w3schools.com/my test.asp?name=ståle&car=saab";
                  //var uri_enc = encodeURIComponent(uri);
                  var uri_dec = decodeURIComponent(uri);
                  
                  $("#source").val(uri_dec);
            })
   }
)
</script>