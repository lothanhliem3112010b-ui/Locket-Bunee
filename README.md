# <!DOCTYPE html>
<html>
<head>
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>Locket Bunee</title>

<style>
body{
font-family: Arial;
background:#111;
color:white;
text-align:center;
margin:0;
}

h1{
padding:20px;
}

video{
width:100%;
max-width:400px;
border-radius:20px;
}

canvas{
display:none;
}

button{
padding:15px 30px;
font-size:18px;
border:none;
border-radius:20px;
background:#ff2d55;
color:white;
margin-top:20px;
}

img{
width:90%;
margin-top:20px;
border-radius:20px;
}
</style>
</head>

<body>

<h1>Locket Bunee 📸</h1>

<video id="video" autoplay playsinline></video>

<br>

<button onclick="takePhoto()">Chụp ảnh</button>

<canvas id="canvas"></canvas>

<div id="photo"></div>

<script>

const video = document.getElementById("video")

navigator.mediaDevices.getUserMedia({
video:true
})
.then(stream=>{
video.srcObject = stream
})

function takePhoto(){

const canvas = document.getElementById("canvas")
const context = canvas.getContext("2d")

canvas.width = video.videoWidth
canvas.height = video.videoHeight

context.drawImage(video,0,0)

const img = canvas.toDataURL("image/png")

document.getElementById("photo").innerHTML =
`<img src="${img}">`

}

</script>

</body>
</html>
