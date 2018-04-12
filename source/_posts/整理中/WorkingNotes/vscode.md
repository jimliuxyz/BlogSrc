---
title: __
---


workbench.action.togglePanel
"cmd+j"
換cmd enter

cursorWordEndRight
opt 右
換:cmd L

cursorWordEndRightSelect
opt sft 右
換:cmd sft L

上下左右
cmd opt 上下左右
換:cmd opt IKJL

# comment
cmd /

# toggle terminal window
cmd J

# new terminal
ctrl `

# show termainl
ctrl ` sft

# beautify
sft opt F

1.檔案需要固定route,加到.angular-cli.json





let mydiv=document.getElementById("mydiv")

el=mydiv;
i=0;

while (el) {
  console.log(i + '. ' + el.nodeName);
  if (el.nodeName=="SCRIPT")
     break;
  
  //if(el.nodeType==3)
  //   console.log(el.childNodes)
  el.childNodes.forEach((v,i)=>{
    //console.dir(v)
    v.style="border:1px solid red"
        console.log(" "+v.nodeName + " " + v.textContent)
  })
     
     
  el = el.nextSibling;
  i++;
}