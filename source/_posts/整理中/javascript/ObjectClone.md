---
title: ObjectClone
---


shallow copy:
拷貝Object:只能拷貝Primitive Type,其他object類( array function date regx )都只能reference
拷貝Array:只能拷貝一維中的Primitive Type.

//slice func
var copyarr = arr.slice(0);

//spread op (拆解再置入)
var copyarr = [...arr];

//assign (也可做合併)
var copyarr = Object.assign([], arr);
var copyobj = Object.assign({}, obj);

---

deep copy:

//純資料複製,function會變成undefined
var copyarr = JSON.parse(JSON.stringify(deepArray))

//jquery
var copyarr = $.extend(true, [], arr);

//lodash
var copyarr = _.cloneDeep(arr);
