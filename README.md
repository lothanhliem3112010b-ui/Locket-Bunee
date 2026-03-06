<!DOCTYPE html>
<html>
<head>

<meta name="viewport" content="width=device-width, initial-scale=1">

<title>Locket Bunee</title>

<style>

body{
margin:0;
font-family:Arial;
background:black;
color:white;
text-align:center;
}

video{
width:100%;
max-width:400px;
border-radius:20px;
margin-top:10px;
}

button{
padding:12px 18px;
border:none;
border-radius:20px;
margin:6px;
font-size:16px;
}

.controls{
margin-top:10px;
}

img{
width:90%;
margin-top:15px;
border-radius:20px;
}

.chatbox{
margin-top:10px;
}

input{
padding:10px;
border-radius:10px;
border:none;
}

.chat{
margin-top:10px;
max-width:400px;
margin-left:auto;
margin-right:auto;
background:#222;
padding:10px;
border-radius:10px;
}

</style>

</head>

<body>

<h2>Locket Bunee 📸</h2>

<video id="video" autoplay playsinline></video>

<canvas id="canvas" style="display:none"></canvas>

<div class="controls">

<button onclick="takePhoto()">📸 Chụp</button>

<button onclick="savePhoto()">💾 Lưu</button>

<button onclick="switchCam()">🔄 Xoay Cam</button>

<button onclick="emoji()">😀 Emoji</button>

</div>

<div id="photo"></div>

<div class="chatbox">

<input id="message" placeholder="Nhắn gì đó...">

<button onclick="sendChat()">💬 Gửi</button>

</div>

<div class="chat" id="chat"></div>

<script>

let video=document.getElementById("video")
let canvas=document.getElementById("canvas")
let currentStream
let facing="user"

function startCam(){

navigator.mediaDevices.getUserMedia({
video:{facingMode:facing}
})

.then(stream=>{

currentStream=stream
video.srcObject=stream

})

}

startCam()

function takePhoto(){

let ctx=canvas.getContext("2d")

canvas.width=video.videoWidth
canvas.height=video.videoHeight

ctx.drawImage(video,0,0)

let img=canvas.toDataURL("image/png")

document.getElementById("photo").innerHTML=
`<img id="captured" src="${img}">`

}

function savePhoto(){

let img=document.getElementById("captured")

if(!img)return

let a=document.createElement("a")
a.href=img.src
a.download="photo.png"
a.click()

}

function switchCam(){

facing = facing=="user" ? "environment":"user"

currentStream.getTracks().forEach(track=>track.stop())

startCam()

}

function emoji(){

let e=prompt("Nhập emoji")

let img=document.getElementById("captured")

if(!img)return

document.getElementById("photo").innerHTML +=
`<div style="font-size:40px">${e}</div>`

}

function sendChat(){

let msg=document.getElementById("message").value

if(msg=="")return

document.getElementById("chat").innerHTML+=
`<p>💬 ${msg}</p>`

document.getElementById("message").value=""

}

</script>

</body>

</html>
