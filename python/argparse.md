# argparse

`formatter_class=argparse.ArgumentDefaultsHelpFormatter` 这个是用来显示默认值的

    import parser
    
    parser = argparse.ArgumentParser(
            formatter_class=argparse.ArgumentDefaultsHelpFormatter,
            description='Demo compare')
            
    parser.add_argument('--method', type=str, default='template', help='Choose one of <pixel|template>')
    parser.add_argument('videopath', type=str, nargs=1)
    args = parser.parse_args()
    
    args.video 是一个数组