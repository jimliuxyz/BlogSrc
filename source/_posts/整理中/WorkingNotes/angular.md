---
title: __
---

angular會自動剔除不需要被編譯的ts檔案
angular universal做server render會在server也跑一次client的js code,要用isPlatformServer做路徑分離

---

npm install -g @angular/cli

npm install bootstrap -save

angular-cli.json : app.styles
    "./../node_modules/bootstrap/dist/css/bootstrap.min.css"

ng new myApp
ng new My_New_Project --style=scss

---

tsconfig.json : "allowJs": true


---

ng g c 'mods/mod-nodereader' --module app.module

--skip-import

---

gulp server
ng build -w

---

npm install bootstrap
npm install jquery

add path to angular.cli.json "style/script"
           "../node_modules/bootstrap/dist/css/bootstrap.min.css"
           "../node_modules/jquery/dist/jquery.min.js"

npm install @types/jquery


add 'jquery' to tsconfig.js "types"
=> not really needed, depending on versions of Typescript

import * as $ from 'jquery';

---






