---
title: __
---


data binding
@input
@output 
[(ngModule)]

---

Template中可透過#做互動

<button (click)="timer.stop()">Stop</button>
<div class="seconds">{{timer.seconds}}</div>
<countdown-timer #timer></countdown-timer>

---


