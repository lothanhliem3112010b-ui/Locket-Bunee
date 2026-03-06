<!DOCTYPE html>
<html lang="vi">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Locket-Bunee</title>

<style>
body{
margin:0;
background:#000;
color:white;
font-family:sans-serif;
text-align:center;
}

video{
width:100%;
height:60vh;
object-fit:cover;
}

.controls{
position:fixed;
bottom:20px;
width:100%;
display:flex;
justify-content:center;
gap:10px;
}

button{
padding:12px 16px;
border:none;
border-radius:20px;
font-size:16px;
}

.capture{background:white;}
.save{background:#00ff88;}
.rotate{background:#ffaa00;}
.emoji{background:#ff66cc;}
.chat{background:#00aaff;}

#chatBox{
position:fixed;
bottom:90px;
left:50%;
transform:translateX(-50%);
width:80%;
display:none;
}

#chatBox input{
width:70%;
padding:10px;
border-radius:10px;
border:none;
}

#chatBox button{
padding:10px;
}
</style>
</head>

<body>

<h2>Locket-Bunee</h2>

<video id="camera" autoplay playsinline></video>
<canvas id="canvas" style="display:none;"></canvas>

<div id="emojiArea"></div>

<div id="chatBox">
<input type="text" id="chatInput" placeholder="Nhắn gì đó...">
<button onclick="sendChat()">Gửi</button>
</div>

<div class="controls">
<button class="capture" onclick="takePhoto()">📷</button>
<button class="save" onclick="savePhoto()">💾</button>
<button class="rotate" onclick="rotateCamera()">🔄</button>
<button class="emoji" onclick="dropEmoji()">😊</button>
<button class="chat" onclick="toggleChat()">💬</button>
</div>

<script>
let video = document.getElementById("camera");
let canvas = document.getElementById("canvas");
let ctx = canvas.getContext("2d");

let useFront=true;

async function startCamera(){
let stream = await navigator.mediaDevices.getUserMedia({
video:{facingMode: useFront ? "user":"environment"}
});
video.srcObject=stream;
}
startCamera();

function rotateCamera(){
useFront=!useFront;
startCamera();
}

function takePhoto(){
canvas.width=video.videoWidth;
canvas.height=video.videoHeight;
ctx.drawImage(video,0,0);
alert("Đã chụp ảnh!");
}

function savePhoto(){
let link=document.createElement("a");
link.download="photo.png";
link.href=canvas.toDataURL();
link.click();
}

function dropEmoji(){
let e=document.createElement("div");
e.innerHTML="😊";
e.style.fontSize="40px";
document.getElementById("emojiArea").appendChild(e);
}

function toggleChat(){
let box=document.getElementById("chatBox");
box.style.display= box.style.display=="none"?"block":"none";
}

function sendChat(){
let input=document.getElementById("chatInput");
alert("Tin nhắn: "+input.value);
input.value="";
}
</script>

</body>
</html>
