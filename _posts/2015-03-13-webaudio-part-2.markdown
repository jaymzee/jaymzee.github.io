---
layout: post
---
#amplitude modulation
<a title="By Berserkerus (Own work) [CC BY-SA 2.5 (http://creativecommons.org/licenses/by-sa/2.5)], via Wikimedia Commons" href="http://commons.wikimedia.org/wiki/File%3AAmfm3-en-de.gif"><img width="256" alt="Amfm3-en-de" src="//upload.wikimedia.org/wikipedia/commons/a/a4/Amfm3-en-de.gif"/></a>

volume:
<input id="volume" type="range" min="0" max="100" value="0">
<span id="volumex"></span><br>
frequency:
<input id="oscfreq" type="range" min="200" max="5000" value="200">
<span id="oscfreqx"></span><br>
LFO frequency:
<input id="lfofreq" type="range" min="1" max="250" value="1">
<span id="lfofreqx"></span><br>
waveform:
<select id="waveform">
  <option value="sine">sine</option>
  <option value="square">square</option>
  <option value="sawtooth">sawtooth</option>
</select>

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

  osc1.connect(gain1);
  gain1.connect(gain2);
  gain2.connect(context.destination);
  lfo1.connect(gain1.gain);

  osc1.start();
  lfo1.type = 'sine';
  lfo1.start();

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
  document.getElementById("waveform").onchange = function () {
    osc1.type = this.value;
  };

  document.getElementById("volume").onchange();
  document.getElementById("oscfreq").onchange();
  document.getElementById("lfofreq").onchange();  
};

</script>