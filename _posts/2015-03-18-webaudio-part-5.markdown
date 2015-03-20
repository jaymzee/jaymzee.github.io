---
layout: post
title: Webaudio - part 5
---
#visualization
volume: <input id="volume" type="range" min="0" max="1" value="0" step=".01">
<span id="volumex"></span><br>
fmod: <input id="fmod" type="range" min="0" max="10000" value="0">
<span id="fmodx"></span><br>
fdev: <input id="fdev" type="range" min="0" max="10000" value="0">
<span id="fdevx"></span><br>
carrier: <input id="carrier" type="range" min="0" max="10000" value="0">
<span id="carrierx"></span><br>
amod: <input id="amod" type="range" min="0" max="10000" value="0">
<span id="amodx"></span><br>
ag: <input id="ag" type="range" min="0" max="1" value="0" step=".01">
<span id="agx"></span><br>

<canvas id="tdplot" width="300" height="150">
  your browser does not support the canvas element
</canvas>

<canvas id="fplot" width="300" height="150">
  your browser does not support the canvas element
</canvas>

<script>
"use strict";

if (typeof AudioContext != "function" && 
  typeof webkitAudioContext == "function") {
  window.AudioContext = webkitAudioContext;
}

window.onload = function () {
  var actx = new AudioContext();
  var fmod = actx.createOscillator();
  var fdev = actx.createGain();
  var carrier = actx.createOscillator();
  var amod = actx.createOscillator();
  var ag = actx.createGain();
  var vol = actx.createGain();

  fmod.connect(fdev);
  fdev.connect(carrier.frequency);
  carrier.connect(ag);
  amod.connect(ag.gain);
  ag.connect(vol);
  vol.connect(actx.destination);

  fmod.start(0);
  carrier.start(0);
  amod.start(0);

  document.getElementById("volume").onchange = function () {
    vol.gain.value = this.value;
    document.getElementById("volumex").innerHTML = this.value;
  }

  document.getElementById("fmod").onchange = function () {
    fmod.frequency.value = this.value;
    document.getElementById("fmodx").innerHTML = this.value;
  }

  document.getElementById("fdev").onchange = function () {
    fdev.gain.value = this.value;
    document.getElementById("fdevx").innerHTML = this.value;
  }

  document.getElementById("carrier").onchange = function () {
    carrier.frequency.value = this.value;
    document.getElementById("carrierx").innerHTML = this.value;
  }

  document.getElementById("amod").onchange = function () {
    amod.frequency.value = this.value;
    document.getElementById("amodx").innerHTML = this.value;
  }

  document.getElementById("ag").onchange = function () {
    ag.gain.value = this.value;
    document.getElementById("agx").innerHTML = this.value;
  }

  document.getElementById("volume").onchange();
  document.getElementById("fmod").onchange();
  document.getElementById("fdev").onchange();
  document.getElementById("carrier").onchange();
  document.getElementById("amod").onchange();
  document.getElementById("ag").onchange();

  
}

  </script>