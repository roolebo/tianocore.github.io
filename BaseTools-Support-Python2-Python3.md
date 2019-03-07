## 1. What BaseTools has changed in order to be compatible with Python2 and Python3?
* If Python2 has a built-in module or its methods that doesn't exist in Python3,replace it with 
another appropriate methods. For example, `'itertools', 'IterableUserDict', 'long', 'reduce',
'argparse.ArgumentParser', 'get_bytes_le', 'iteritems', 'xrange'`.
* If Python2 and Python3 have the same built-in modules and methods, but the use of the module 
or method or the output data is inconsistent, other means or constraints are needed to make it 
consistent. For example, `'map', 'super', 'division', Sort dict and set, Dict attribute such as 
'dict.items', 'dict.values', 'dict.keys'`.
* Some of the changes are due to problems with Python2 and Python3 coding.
For example, File IO operations, data output from the uuid module, Bytes string, `'Struck.pack', 'Struck.unpack'`.
* Update windows and linux run scripts file.
The main solution is to choose the Python version independently.


## 2. How is each problem solved?
### If Python2 has a built-in module or its methods that doesn't exist in Python3,replace it with another appropriate methods.
* `'long':              'long' change to 'int'`
* `'get_bytes_le':      'get_bytes_le' change to 'bytes_le'`
* `'iteritems':         'iteritems' change to 'items'`
* `'xrange':            'xrange' change to 'range'`
* `'itertools':         'itertools.ifilter' change to 'filter','itertools.imap' change to 'map'.You can also use loop statements`
* `'IterableUserDict':  'from UserDict import IterableUserDict' change to 'from collections import OrderedDict'`
* `'reduce':             Reduce is a built-in module in Python2 but it needs to be imported in Python3. 'from functools import reduce'`
* `'ArgumentParser':     Python3 is missing the 'version' parameter from Python2.Define this argument yourself using 'add argument', Parser.add_argument("--version", action='version', version=__version__)`

### If Python2 and Python3 have the same built-in modules and methods, but the use of the module or method or the output data is inconsistent, other means or constraints are needed to make it consistent.
*  `'map':               'map(argument)' change to 'list(map(argument))'`
*  `'super':              You can delete it directly or override the methods of the parent class`
*  `'division':          '//' change to '/'`
*  `'Dict attribute':    'dict.values' change to 'list(dict.values)', 'dict.items' change to 'list(dict.items)', etc`

### Some of the changes are due to problems with Python2 and Python3 coding.
* File IO operations:   Note the use of `"w" and "wb","r" and "rb". "codecs.open","io.open"` can specify the encoding of the open file.  
* SaveFileOnChange():   The third parameter determines the write mode for "w" or "wb".
* uuid module:         `'uuid.UUID(Value).bytes_le'` It's a byte in Python3 but it's a string in Python2.
* Bytes string:        `BytesIO('') change to BytesIO()`. You can also use lists instead of BytesIO or StringIO. Some of the strings that you write in your program need to be preceded by "b" or use the bytearray() function. When Python3 uses the str() method, the result begins with "b'".
* Struck:              `'unpack' and 'pack'` can specify the encoding of the data.

### Update windows and linux run scripts file.
* PYTHON_COMMAND is used within the program as the python application and can be specified directly.
For example, PYTHON_COMMAND=C:\Python27\python.exe 
* Use Python3 based on PYTHON3_ENABLE environment. if PYTHON3_ENABLE is equal to the TRUE, PYTHON_COMMAND is 
automatically set to Python3 applications. If PYTHON3_ENABLE is set to a different value, the Python2 
setup process will be validated. Python3 is used by default if PYTHON3_ENABLE is not defined.
* If tool is applied to Windows system, using Python2 applications must set PYTHON3_ENABLE not equal to TRUE 
and then set PYTHON_HOME in the same way as before.
* If tool is applied to Linux system, using Python2 applications must set PYTHON3_ENABLE not equal to TRUE.
