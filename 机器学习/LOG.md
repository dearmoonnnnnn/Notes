# 将控制台输出保存在日志文件中

### 导入相关包

```
import sys
import os
import time 

class Logger(object):
    def __init__(self, file_name="Default.log", stream=sys.stdout):
        self.terminal = stream
        self.log = open(file_name, "a")

    def write(self, message):
        self.terminal.write(message)
        self.log.write(message)

    def flush(self):
        pass
        
output_file = './Outputs/' + time.strftime("%Y%m%d-%H%M%S", time.localtime()) + '/'
os.makedirs(output_file) # 创建文件目录

log_file_name = output_file + 'output.log'

# 记录正常的print信息
sys.stdout = Logger(log_file_name)
# 记录traceback 异常信息
# sys.err = Logger(log_file_name)

```

### 