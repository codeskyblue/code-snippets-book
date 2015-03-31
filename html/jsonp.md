# Jsonp的使用

使用jquery比较方便， 后面只要加上`?callback=?`,jquery会帮你处理完其他的事情
```
$.getJSON("http://10.246.13.180:5000/jsonp?callback=?", function(data){
    console.log(data);
    renderChart(data.data);
});
```