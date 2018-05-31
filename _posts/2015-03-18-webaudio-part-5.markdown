---
layout: post
title: Webaudio - part 5
---
# visualization of audio signals
volume: <input id="volume" type="range" min="0" max="1" value="0" step=".01">
<span id="volumex"></span><br>
fmod: <input id="fmod" type="range" min="0" max="4000" value="0">
<span id="fmodx"></span><br>
fdev: <input id="fdev" type="range" min="0" max="4000" value="0">
<span id="fdevx"></span><br>
carrier: <input id="carrier" type="range" min="0" max="4000" value="0">
<span id="carrierx"></span><br>
amod: <input id="amod" type="range" min="0" max="4000" value="0">
<span id="amodx"></span><br>
ag: <input id="ag" type="range" min="0" max="1" value="0" step=".01">
<span id="agx"></span><br>

# oscilloscope and frequency spectrum
<canvas id="tdplot" width="300" height="150">
  your browser does not support the canvas element
</canvas> 

<canvas id="fplot" width="300" height="150">
  your browser does not support the canvas element
</canvas>

# node graph
{% highlight javascript %}
fmod.connect(fdev);
fdev.connect(carrier.frequency);
carrier.connect(ag);
amod.connect(ag.gain);
ag.connect(analyser);
analyser.connect(vol);
vol.connect(actx.destination);
{% endhighlight %}

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
  var analyser = actx.createAnalyser();

  fmod.connect(fdev);
  fdev.connect(carrier.frequency);
  carrier.connect(ag);
  amod.connect(ag.gain);
  ag.connect(analyser);
  analyser.connect(vol);
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

  var tdplot = document.getElementById("tdplot");
  var fplot = document.getElementById("fplot");

  function draw() {
    var tdctx = tdplot.getContext("2d");
    var fctx = fplot.getContext("2d");
    var tdLength = analyser.fftSize;
    var fLength = analyser.frequencyBinCount;
    var tdData = new Uint8Array(tdLength);
    var fData = new Uint8Array(fLength);
    var x, sliceWidth;
    
    tdctx.fillStyle = 'rgb(240, 240, 240)';
    tdctx.fillRect(0, 0, tdplot.width, tdplot.height);

    analyser.getByteTimeDomainData(tdData);
    sliceWidth = tdplot.width / tdLength;
    x = 0;
    tdctx.beginPath();
    for (var i = 0; i < tdLength; i++) {
      var y = tdplot.height / 2.0 * ((255 - tdData[i]) / 128.0);
      if (i == 0) {
        tdctx.moveTo(x, y);
      } else {
        tdctx.lineTo(x, y);
      }
      x += sliceWidth;
    }    
    tdctx.stroke();

    fctx.fillStyle = 'rgb(240, 240, 240)';
    fctx.fillRect(0, 0, tdplot.width, tdplot.height);

    analyser.getByteFrequencyData(fData);
    sliceWidth = fplot.width / fLength;
    x = 0;
    fctx.beginPath();
    for (var i = 0; i < fLength; i++) {
      var y = fplot.height / 2.0 * ((255 - fData[i]) / 128.0);
      if (i == 0) {
        fctx.moveTo(x, y);
      } else {
        fctx.lineTo(x, y);
      }
      x += sliceWidth;
    }
    fctx.stroke();

    window.requestAnimationFrame(draw);
  }

  analyser.fftSize = 512;

  window.requestAnimationFrame(draw);
}
</script>