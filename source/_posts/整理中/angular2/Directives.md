---
title: __
---

https://dotblogs.com.tw/wellwind/2017/02/14/angular2-directive-intro

分三種
Components:即DOM element
Structural:改變DOM element結構,像是ngIf與ngFor
Attribute:改變DOM element的顯示或行為,可以當作一種擴充與重複利用的概念

---
ng-content
將HTML_CONTENT傳送給component

mytpl:
<subcomp>
	HTML_CONTENT
</subcomp>

subcomp-tpl:
<div>
	<!-- HTML_CONTENT會被插入到這裡 -->
	<ng-content ><ng-content>
</div>

---

用select指定要插入的component(類似css的selector)


      <my-component>
        <div mycardtype1>
          A
        </div>
        <div class="mycardtype2" style='color:red'>
          B
        </div>
        <div mycardtype3=1>
          C
        </div>
      </my-component>




    <div class="my-component">
      <div>
        Title:
        <ng-content select="[mycardtype1]"></ng-content>
      </div>
      <div>
        Content:
        <ng-content select=".mycardtype2"></ng-content>
      </div>
      <div>
        Content:
        <ng-content select="[mycardtype3=1]"></ng-content>
      </div>
    </div>


Title:
A
Content:
B
Content:
C

---
