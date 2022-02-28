<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>Document</title>
	<!--Подключаем библиотеку-->
	<script src="https://ajax.googleapis.com/ajax/libs/jquery/2.2.0/jquery.min.js"></script>
<div id="app-cover">
<div id="bg-artwork"></div>
<div id="bg-layer"></div>
<div id="player">
<div id="player-track">
<div id="album-name"></div>
<div id="track-name"></div>
<div id="track-time">
<div id="current-time"></div>
<div id="track-length"></div>
</div>
<div id="s-area">
<div id="ins-time"></div>
<div id="s-hover"></div>
<div id="seek-bar"></div>
</div>
</div>
<div id="player-content">
<div id="album-art">
<img src="img/1.jpg" class="active" id="_1">
<img src="img/2.jpg" id="_2">
<img src="img/3.jpg" id="_3">
<img src="img/4.jpg" id="_4">
<img src="img/5.jpg" id="_5">
<div id="buffer-box">Обработка ...</div>
</div>
<div id="player-controls">
<div class="control">
<div class="button" id="play-previous">
<i class="fas fa-backward"></i>
</div>
</div>
<div class="control">
<div class="button" id="play-pause-button">
<i class="fas fa-play"></i>
</div>
</div>
<div class="control">
<div class="button" id="play-next">
<i class="fas fa-forward"></i>
</div>
</div>
</div>
</div>
</div>
</div>
 #app-cover
{
position: absolute;
top: 50%;
right: 0;
left: 0;
width: 430px;
height: 100px;
margin: -4px auto;
}
 
#bg-artwork
{
position: fixed;
top: -30px;
right: -30px;
bottom: -30px;
left: -30px;
background-image: url(../img/1.jpg);
background-repeat: no-repeat;
background-size: cover;
background-position: 50%;
filter: blur(40px);
-webkit-filter: blur(40px);
z-index: 1;
}
 
#bg-layer
{
position: fixed;
top: 0;
right: 0;
bottom: 0;
left: 0;
background-color: #fff;
opacity: 0.51;
z-index: 2;
}
 #player
{
position: relative;
height: 100%;
z-index: 3;
}
 
#player-track
{
position: absolute;
top: 0;
right: 15px;
left: 15px;
padding: 13px 22px 10px 184px;
background-color: #fff7f7;
border-radius: 15px 15px 0 0;
transition: 0.3s ease top;
z-index: 1;
}
 
#player-track.active
{
top: -92px;
}
 #album-name
{
color: #54576f;
font-size: 17px;
font-weight: bold;
}
 
#track-name
{
color: #acaebd;
font-size: 13px;
margin: 2px 0 13px 0;
}
 
#track-time
{
height: 12px;
margin-bottom: 3px;
overflow: hidden;
}
 
#current-time
{
float: left;
}
 
#track-length
{
float: right;
}
 
#current-time, #track-length
{
color: transparent;
font-size: 11px;
background-color: #ffe8ee;
border-radius: 10px;
transition: 0.3s ease all;
}
 
#track-time.active #current-time, #track-time.active #track-length
{
color: #f86d92;
background-color: transparent;
}
 #album-art
{
position: absolute;
top: -40px;
width: 115px;
height: 115px;
margin-left: 40px;
-webkit-transform: rotateZ(0);
transform: rotateZ(0);
transition: 0.3s ease all;
box-shadow: 0 0 0 10px #fff;
border-radius: 50%;
overflow: hidden;
}
 
#album-art.active
{
top: -60px;
box-shadow: 0 0 0 4px #fff7f7, 0 30px 50px -15px #afb7c1;
}
 
#album-art:before
{
content: '';
position: absolute;
top: 50%;
right: 0;
left: 0;
width: 20px;
height: 20px;
margin: -10px auto 0 auto;
background-color: #d6dee7;
border-radius: 50%;
box-shadow: inset 0 0 0 2px #fff;
z-index: 2;
}
 
#album-art img
{
display: block;
position: absolute;
top: 0;
left: 0;
width: 100%;
height: 100%;
opacity: 0;
z-index: -1;
}
 
#album-art img.active
{
opacity: 1;
z-index: 1;
}
 #player-controls
{
width: 250px;
height: 100%;
margin: 0 5px 0 141px;
float: right;
overflow: hidden;
}
 
.control
{
width: 33.333%;
float: left;
padding: 12px 0;
}
 
.button
{
width: 26px;
height: 26px;
padding: 25px;
background-color: #fff;
border-radius: 6px;
cursor: pointer;
}
 
.button i
{
display: block;
color: #d6dee7;
font-size: 26px;
text-align: center;
line-height: 1;
}
 
.button, .button i
{
transition: 0.2s ease all;
}
 
.button:hover
{
background-color: #d6d6de;
}
 
.button:hover i
{
color: #fff;
}
 $(function()
{
var playerTrack = $("#player-track"), bgArtwork = $('#bg-artwork'), bgArtworkUrl, albumName = $('#album-name'), trackName = $('#track-name'), albumArt = $('#album-art'), sArea = $('#s-area'), seekBar = $('#seek-bar'), trackTime = $('#track-time'), insTime = $('#ins-time'), sHover = $('#s-hover'), playPauseButton = $("#play-pause-button"), i = playPauseButton.find('i'), tProgress = $('#current-time'), tTime = $('#track-length'), seekT, seekLoc, seekBarPos, cM, ctMinutes, ctSeconds, curMinutes, curSeconds, durMinutes, durSeconds, playProgress, bTime, nTime = 0, buffInterval = null, tFlag = false, albums = ['Dawn','Me & You','Electro Boy','Home','Proxy (Original Mix)'], trackNames = ['Skylike - Dawn','Alex Skrindo - Me & You','Kaaze - Electro Boy','Jordan Schor - Home','Martin Garrix - Proxy'], albumArtworks = ['_1','_2','_3','_4','_5'], trackUrl = ['../mp3/2.mp3','../mp3/1.mp3','../mp3/3.mp3','../mp3/4.mp3','../mp3/5.mp3'], playPreviousTrackButton = $('#play-previous'), playNextTrackButton = $('#play-next'), currIndex = -1;
 
function playPause()
{
setTimeout(function()
{
if(audio.paused)
{
playerTrack.addClass('active');
albumArt.addClass('active');
checkBuffering();
i.attr('class','fas fa-pause');
audio.play();
}
else
{
playerTrack.removeClass('active');
albumArt.removeClass('active');
clearInterval(buffInterval);
albumArt.removeClass('buffering');
i.attr('class','fas fa-play');
audio.pause();
}
},300);
}function showHover(event)
{
seekBarPos = sArea.offset();
seekT = event.clientX - seekBarPos.left;
seekLoc = audio.duration * (seekT / sArea.outerWidth());
sHover.width(seekT);
cM = seekLoc / 60;
ctMinutes = Math.floor(cM);
ctSeconds = Math.floor(seekLoc - ctMinutes * 60);
if( (ctMinutes < 0) || (ctSeconds < 0) )
return;
if( (ctMinutes < 0) || (ctSeconds < 0) )
return;
if(ctMinutes < 10)
ctMinutes = '0'+ctMinutes;
if(ctSeconds < 10)
ctSeconds = '0'+ctSeconds;
if( isNaN(ctMinutes) || isNaN(ctSeconds) )
insTime.text('--:--');
else
insTime.text(ctMinutes+':'+ctSeconds);
insTime.css({'left':seekT,'margin-left':'-21px'}).fadeIn(0);
}
 
function hideHover()
{
sHover.width(0);
insTime.text('00:00').css({'left':'0px','margin-left':'0px'}).fadeOut(0);
}
function playFromClickedPos()
{
audio.currentTime = seekLoc;
seekBar.width(seekT);
hideHover();
}
 
function updateCurrTime()
{
nTime = new Date();
nTime = nTime.getTime();
 
if( !tFlag )
{
tFlag = true;
trackTime.addClass('active');
}
 
curMinutes = Math.floor(audio.currentTime / 60);
curSeconds = Math.floor(audio.currentTime - curMinutes * 60);
durMinutes = Math.floor(audio.duration / 60);
durSeconds = Math.floor(audio.duration - durMinutes * 60);
playProgress = (audio.currentTime / audio.duration) * 100;
if(curMinutes < 10)
curMinutes = '0'+curMinutes;
if(curSeconds < 10)
curSeconds = '0'+curSeconds;
if(durMinutes < 10)
durMinutes = '0'+durMinutes;
if(durSeconds < 10)
durSeconds = '0'+durSeconds;
if( isNaN(curMinutes) || isNaN(curSeconds) )
tProgress.text('00:00');
else
tProgress.text(curMinutes+':'+curSeconds);
if( isNaN(durMinutes) || isNaN(durSeconds) )
tTime.text('00:00');
else
tTime.text(durMinutes+':'+durSeconds);
if( isNaN(curMinutes) || isNaN(curSeconds) || isNaN(durMinutes) || isNaN(durSeconds) )
trackTime.removeClass('active');
else
trackTime.addClass('active');seekBar.width(playProgress+'%');
if( playProgress == 100 )
{
i.attr('class','fa fa-play');
seekBar.width(0);
tProgress.text('00:00');
albumArt.removeClass('buffering').removeClass('active');
clearInterval(buffInterval);
}
}
function checkBuffering()
{
clearInterval(buffInterval);
buffInterval = setInterval(function()
{
if( (nTime == 0) || (bTime - nTime) > 1000 )
albumArt.addClass('buffering');
else
albumArt.removeClass('buffering');
 
bTime = new Date();
bTime = bTime.getTime();
 
},100);
}
 
function selectTrack(flag)
{
if( flag == 0 || flag == 1 )
++currIndex;
else
--currIndex;
 
if( (currIndex > -1) && (currIndex < albumArtworks.length) )
{
if( flag == 0 )
i.attr('class','fa fa-play');
else
{
albumArt.removeClass('buffering');
i.attr('class','fa fa-pause');
}
 
seekBar.width(0);
trackTime.removeClass('active');
tProgress.text('00:00');
tTime.text('00:00');
 
currAlbum = albums[currIndex];
currTrackName = trackNames[currIndex];
currArtwork = albumArtworks[currIndex];
 
audio.src = trackUrl[currIndex];
 
nTime = 0;
bTime = new Date();
bTime = bTime.getTime();
 
if(flag != 0)
{
audio.play();
playerTrack.addClass('active');
albumArt.addClass('active');
 
clearInterval(buffInterval);
checkBuffering();
}
 
albumName.text(currAlbum);
trackName.text(currTrackName);
albumArt.find('img.active').removeClass('active');
$('#'+currArtwork).addClass('active');
 
bgArtworkUrl = $('#'+currArtwork).attr('src');
 
bgArtwork.css({'background-image':'url('+bgArtworkUrl+')'});
}
else
{
if( flag == 0 || flag == 1 )
--currIndex;
else
++currIndex;
}
}
 
function initPlayer()
{
audio = new Audio();
 
selectTrack(0);
audio.loop = false;
playPauseButton.on('click',playPause);
sArea.mousemove(function(event){ showHover(event); });
sArea.mouseout(hideHover);
sArea.on('click',playFromClickedPos);
$(audio).on('timeupdate',updateCurrTime);
 
playPreviousTrackButton.on('click',function(){ selectTrack(-1);} );
playNextTrackButton.on('click',function(){ selectTrack(1);});
}
initPlayer();
});
 </head>
  <style>
body {
    background: #c7b39b url(https://github.com/Rams1989/music_base/blob/main/annotaciya-muzykalnogo-rabochego-stola.jpg?raw=true); /* Цвет фона и путь к файлу */
   background-size: cover; 
-moz-background-size: cover; 
-o-background-size: cover; 
-webkit-background-size: cover;		
   }
   </style>
