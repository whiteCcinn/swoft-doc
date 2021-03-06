# 进程

swoft提供自定义进程，每个进程swoft启动的时候一起启动，manager进程负责管理该进程，默认启动的进程是守护进程，并且意外退出后，manager进程会重新启动一个。

## 定义进程

定义进程简单，只需继承Swoft\Process\AbstractProcess类，实现run方法，run方法里面就是实际逻辑。自定义进程和任务一样，里面可以使用所有功能比如注解、日志、rpc、http、mysql、redis底层无缝自动切换成同步IO操作。

```php
<?php

namespace App\Process;

use Swoft\App;
use Swoft\Process\AbstractProcess;
use Swoole\Process;

/**
 * 自定义进程demo
 *
 * @uses      MyProcess
 * @version   2017年10月02日
 * @author    stelin <phpcrazy@126.com>
 * @copyright Copyright 2010-2016 swoft software
 * @license   PHP Version 7.x {@link http://www.php.net/license/3_0.txt}
 */
class MyProcess extends AbstractProcess
{
    /**
     * 实际进程运行逻辑
     *
     * @param Process $process 进程对象
     */
    public function run(Process $process)
    {
        $pname = $this->server->getPname();
        $processName = "$pname myProcess process";
        $process->name($processName);

        $i = 1;
        while (true) {
            echo "this my process \n";
            App::trace("my process count=" . $i);
            sleep(10);
            $i++;
        }
    }
}
```

 

## 配置进程

swoft.ini里面配置进程，server start的时候，就会启动改进程，必须为每一个进程定义一个名称，用于进程之间通讯。

```py
[process]
reload = 'Swoft\Process\ReloadProcess'
myProcess = 'App\Process\MyProcess'
```



