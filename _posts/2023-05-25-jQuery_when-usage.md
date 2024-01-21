---
layout: post
title: "jQuery.when的使用"
author: "Kai"
header-style: text
tags:
  - jQuery
  - 异步
---

需求：有1000个合同，每次并发处理2个合同（单独对每个合同调用WebAPI接口），每次确保2个合同被处理完毕后等待1秒再继续处理后面2个。
```javascript
// 1000个合同的数组
var contracts = [...];

// 记录当前处理的合同位置
var currentPosition = 0;

// 处理两个合同并调用Web API接口
function processContracts() {
  // 取出两个合同
  var contract1 = contracts[currentPosition];
  var contract2 = contracts[currentPosition + 1];

  // 分别处理两个合同的接口调用
  var contract1Ajax = $.ajax({
    url: "yourWebAPIUrl",
    method: "POST",
    data: { contractData: contract1 }
  });

  var contract2Ajax = $.ajax({
    url: "yourWebAPIUrl",
    method: "POST",
    data: { contractData: contract2 }
  });

  // 等待两个接口调用都完成后，再处理下两个合同
  $.when(contract1Ajax, contract2Ajax).then(function(contract1Result, contract2Result) {
    // 处理合同1的接口调用结果
    console.log("合同1的接口调用结果：" + contract1Result);
    // 处理合同2的接口调用结果
    console.log("合同2的接口调用结果：" + contract2Result);

    // 延迟1秒后，处理下两个合同
    setTimeout(function() {
      currentPosition += 2;
      if (currentPosition < contracts.length) {
        processContracts();
      }
    }, 1000);
  });
}

// 开始处理合同
processContracts();
```

在这个代码中，我们首先使用 $.ajax 函数分别调用了两个合同的 Web API 接口。由于这两个接口是并行调用的，因此需要使用 $.when 函数来等待两个接口都执行完成后再处理下两个合同的接口调用。

当两个接口调用都成功完成后，$.when 函数会执行 then 函数里的回调函数，我们可以在该函数中处理两个接口调用的结果。处理完成后，会先等待 1 秒，再更新 currentPosition 的值，递归调用 processContracts() 函数处理下两个合同的接口调用。如果所有合同的接口调用都完成了，则递归调用结束。

另一种方式：异步队列
```javascript
// 1000个合同的数组
var contracts = [...];

// 记录当前处理的合同位置
var currentPosition = 0;

$.ajaxPrefilter(function (options, originalOptions, jqXHR) {
  options.async = true;
  options.queue = 'asyncRequests';
});

$.xhrPool = [];
$.xhrPool.abortAll = function () {
  $(this).each(function (idx, jqXHR) {
    jqXHR.abort();
  });
  $.xhrPool.length = 0;
};

$(document).ajaxComplete(function (event, jqXHR, options) {
  var index = $.xhrPool.indexOf(jqXHR);
  if (index > -1) {
    $.xhrPool.splice(index, 1);
  }
});

$(document).ajaxError(function (event, jqXHR, settings, thrownError) {
  if (thrownError !== 'abort') {
    alert('An error occurred while making the ajax request.');
  }
});

function processContracts() {
  var contract1 = contracts[currentPosition];
  var contract2 = contracts[currentPosition + 1];

  var contract1Ajax = $.ajax({
    url: "yourWebAPIUrl",
    method: "POST",
    data: { contractData: contract1 }
  });

  var contract2Ajax = $.ajax({
    url: "yourWebAPIUrl",
    method: "POST",
    data: { contractData: contract2 }
  });

  // 添加 ajax 请求到队列中
  $.xhrPool.push(contract1Ajax);
  $.xhrPool.push(contract2Ajax);

  // 等待两个接口调用都完成后，再处理下两个合同
  $.when(contract1Ajax, contract2Ajax).then(function(contract1Result, contract2Result) {
    // 处理合同1的接口调用结果
    console.log("合同1的接口调用结果：" + contract1Result);
    // 处理合同2的接口调用结果
    console.log("合同2的接口调用结果：" + contract2Result);

    // 延迟1秒后，处理下两个合同
    setTimeout(function() {
      currentPosition += 2;
      if (currentPosition < contracts.length) {
        processContracts();
      }
    }, 1000);
  });
}

// 设置最大并发数
$(document).queue('asyncRequests', function () {
  $.when(processContracts())
    .always(function () {
      $(document).dequeue('asyncRequests');
    });
});

// 开始处理合同
processContracts();
```