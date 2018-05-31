---
layout: post
title: Webaudio part 3 - frequency modulation
---
# frequency modulation
<a title="By Berserkerus (Own work) [CC BY-SA 2.5 (http://creativecommons.org/licenses/by-sa/2.5)], via Wikimedia Commons" href="http://commons.wikimedia.org/wiki/File%3AAmfm3-en-de.gif"><img width="256" alt="Amfm3-en-de" src="//upload.wikimedia.org/wikipedia/commons/a/a4/Amfm3-en-de.gif"/></a>

volume:
<input id="volume" type="range" min="0" max="100" value="0">
<span id="volumex"></span><br>
carrier frequency:
<input id="oscfreq" type="range" min="200" max="5000" value="200">
<span id="oscfreqx"></span> Hz<br>
modulation frequency:
<input id="lfofreq" type="range" min="1" max="250" value="1">
<span id="lfofreqx"></span> Hz<br>
modulation deviation:
<input id="fmgain" type="range" min="0" max="100" value="0" step="0.01">
<span id="fmgainx"></span> Hz<br>

Not hearing anything? 
Please use a modern browser that supports the Web Audio API.

<script type="text/javascript">

"use strict";

if (typeof AudioContext != "function" && 
  typeof webkitAudioContext == "function") {
  window.AudioContext = webkitAudioContext;
}

window.onload = function () {
  var context = new AudioContext();
  var osc1 = context.createOscillator();
  var lfo1 = context.createOscillator();
  var gain1 = context.createGain();
  var gain2 = context.createGain();
  
  lfo1.connect(gain1);
  gain1.connect(osc1.frequency);
  osc1.connect(gain2);
  gain2.connect(context.destination);

  osc1.start(0);
  lfo1.start(0);

  document.getElementById("volume").onchange = function () {
    gain2.gain.value = this.value / 100;
    document.getElementById("volumex").innerHTML = this.value;
  };
  document.getElementById("oscfreq").onchange = function () {    
    osc1.frequency.setValueAtTime(this.value, context.currentTime);
    document.getElementById("oscfreqx").innerHTML = this.value;
  };
  document.getElementById("lfofreq").onchange = function () {    
    lfo1.frequency.setValueAtTime(this.value, context.currentTime);
    document.getElementById("lfofreqx").innerHTML = this.value;
  };
  document.getElementById("fmgain").onchange = function () {    
    gain1.gain.setValueAtTime(this.value, context.currentTime);
    document.getElementById("fmgainx").innerHTML = this.value;
  };

  document.getElementById("volume").onchange();
  document.getElementById("oscfreq").onchange();
  document.getElementById("lfofreq").onchange();
  document.getElementById("fmgain").onchange();
};

</script>