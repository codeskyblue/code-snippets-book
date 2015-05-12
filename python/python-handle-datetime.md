# python时间处理

参考：<http://my.oschina.net/1123581321/blog/169777>

## 当前时间戳

    time.time()
    """result
    1382067918.718
    """
    
## 字符串转 datetime

    datetime.datetime.strptime("2011-12-28 13:11:12", "%Y-%m-%d %X")
     
    """result
    datetime.datetime(2011, 12, 28, 13, 11, 12)
    """

## datetime转字符串
