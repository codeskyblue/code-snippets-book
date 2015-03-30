# Highchart-basicline

Body的内容

首先页面中需要有scipt
```
<script src="highstock.js"></script>
```

body标签里面在加上个div
```
<div id="chart1" style="height: 400px; min-width: 310px"></div>
```

然后是scirpt
```
$(function() {
    // Create the chart
    $('#chart1').highcharts('StockChart', {

        chart: {
            zoomType: "x",  /* 支持选中放大一块区域，值也可以是xy */
        },
        rangeSelector: {
            selected: 1
        },
        title: {
            text: 'Frame'
        },
        plotOptions: {
            line: {
                pointInterval: 1000/30,
            }
        },
        yAxis: {
            min: 0,
            max: 100
        },
        series: [{
            name: '相似度',
            data: [0, 5, 3, 5, 1, 10, 3, 10],
            tooltip: {
                valueDecimals: 0,
                xDateFormat: "%M:%S.%L",
                pointFormat: "{series.name}: {point.y}%"
            },
            marker: {
                enabled: true,
                radius: 3,
                fillColor: "#888"
            }
        }]
    });
});
```