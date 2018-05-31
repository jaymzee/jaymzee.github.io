---
layout: post
title: Webaudio - part 4 - audio synthesis
---
# Envelopes
without an envelope, the sound would seem very abrupt
(turning on then turning off).

For most synthesizers there are at least 4 parameters to adjust the envelope:

 * A - attack - the rate at which the sound gets to it's loudest
 * D - decay - rate at which it diminishes after initial attack
 * S - sustain - how loud the sound is while you hold the key
 * R - release - rate at which the sound diminsishes to zero after key release

Use the home row of keys on your keyboard to play the synth.

     w   r t   u i o
    a s d f g h j k l ;


<script>

"use strict";

if (typeof AudioContext != "function" && 
  typeof webkitAudioContext == "function") {
  window.AudioContext = webkitAudioContext;
}

function Note(frequency) {
  this.frequency = frequency;
  this.attack = .1;
  this.decay = .1;
  this.sustain = .6;
  this.release = .5;
  this.waveform = 'sawtooth';
  this.pressed = false;
}

Note.prototype.noteOn = function () {
  this.pressed = true;
  // find free voice
  voices.sort(function (a, b) {
    return a.retired - b.retired;
  });
  this.voice = voices[0];  
  this.voice.noteOn(this);
};

Note.prototype.noteOff = function () {
  this.voice.noteOff(this);
  this.pressed = false;
};

function Voice(context, destination) {  
  var env = context.createGain();
  var osc; //oscillator
  env.gain.value = 0;
  env.connect(destination);
  this.retired = 0;

  this.noteOn = function (note) {
    var now; 
    if (osc) {
      osc.stop(0); //make sure old oscillator is stopped
    }
    osc = context.createOscillator();
    osc.connect(env);
    osc.frequency.value = note.frequency;
    osc.type = note.waveform;

    now = context.currentTime;
    this.retired = Number.MAX_VALUE;
    env.gain.cancelScheduledValues(now);
    env.gain.linearRampToValueAtTime(1.0, now + note.attack);
    env.gain.linearRampToValueAtTime(note.sustain, 
                                       now + note.attack + note.decay);
    osc.start(now);
  }

  this.noteOff = function (note) {
    var now = context.currentTime;
    env.gain.setValueAtTime(note.sustain, now);
    env.gain.linearRampToValueAtTime(0.0, now + note.release);
    osc.stop(now + note.release);
    this.retired = now + note.release; 
  }
}

var notes = Array(128);
var voices = Array(5);
var keys = [];

window.onload = function () {
  var context = new AudioContext();
  var semi = Math.pow(2, 1 / 12);
  
  var f, i;

  f = 440;
  for (var i = 69; i < 128; i++) {
    notes[i] = new Note(f);
    f = f * semi;
  }
  f = 440;
  for (var i = 68; i >= 0; i--) {
    f = f / semi;
    notes[i] = new Note(f);
  }

  var volume = context.createGain();
  volume.connect(context.destination);
  volume.gain.value = 0.5;

  for (i = 0; i < voices.length; i++) {
    voices[i] = new Voice(context, volume);
  }

  keys[65] = 69; //'A' = A4
  keys[87] = 70; //'W' = A#4
  keys[83] = 71; //'S' = B4
  keys[68] = 72; //'D' = C4
  keys[82] = 73; //'R' = C#4
  keys[70] = 74; //'F' = D4
  keys[84] = 75; //'T' = D#4
  keys[71] = 76; //'G' = E4
  keys[72] = 77; //'H' = F4
  keys[85] = 78; //'U' = F#4
  keys[74] = 79; //'J' = G4
  keys[73] = 80; //'I' = G#4
  keys[75] = 81; //'K' = A5
  keys[79] = 82; //'O' = A#5
  keys[76] = 83; //'L' = B5
  keys[59] = 84; //';' = C5
  
  window.onkeydown = function (ev) {
    var noteNumber = keys[ev.which];
    if (noteNumber != undefined && !notes[noteNumber].pressed) {
      console.log("note " + noteNumber + " pressed");
      notes[noteNumber].noteOn();
    }
  }

  window.onkeyup = function (ev) {
    var noteNumber = keys[ev.which];
    if (noteNumber != undefined && notes[noteNumber].pressed) {
      console.log("note " + noteNumber + " released");
      notes[noteNumber].noteOff(); 
    }
  }
  
};

</script>