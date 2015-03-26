# Flask上传文件

函数`secure_filename`用来将空格转化成`_`, 另外排除掉`/`

具体实现参考 [secure_filename的实现](http://werkzeug.pocoo.org/docs/0.10/utils/#werkzeug.utils.secure_filename)

```
#! /usr/bin/env python
# -*- coding: utf-8 -*-
#
# upload demo

import os
import flask
from flask import request
from werkzeug import secure_filename

app = flask.Flask(__name__)
app.config['UPLOAD_FOLDER'] = 'tmp'
app.config['MAX_CONTENT_LENGTH'] = 16<<20  # max upload size < 16M

@app.route('/', methods=['POST'])
def upload_file():
    file = request.files['file']
    filename = secure_filename(file.filename)
    file.save(os.path.join(app.config['UPLOAD_FOLDER'], filename))
    return 'upload success'

@app.route('/', methods=['GET'])
def explore():
    ''' server upload '''
    return ''' 
<!doctype html>
<title>Upload new File</title>
<h1>Upload new File</h1>
<form action="" method=post enctype=multipart/form-data>
  <p><input type=file name=file>
     <input type=submit value=Upload>
</form>
'''

if __name__ == '__main__':
    app.run(host='0.0.0.0', debug=True)
```
