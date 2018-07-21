```python
    logging.basicConfig(filename='mylog.log', level = logging.DEBUG,filemode='a', datefmt='"%Y/%m/%d/ %H:%M:%S "', format='%(asctime)s -- %(levelname)s -- %(message)s',   )
    logging.info("{}----{}".format(a,a+1))
```