---
title: "python 关闭子线程"
date: 2022-11-16T09:03:33+08:00
draft: False
tags : ['python', 'threading']
Categories : ['python']
---

## 关闭子线程

```python
from threading import Thread
from ctypes import c_long, pythonapi, py_object


class thread(Thread):
    def __init__(self, group=None, target=None, name=None, args=(), kwargs=None, *, daemon=None):
        super().__init__(group, target, name, args, kwargs, daemon=daemon)

    def stop(self):
        tid = c_long(self.ident)
        res = pythonapi.PyThreadState_SetAsyncExc(tid, py_object(SystemExit))
        if res == 0:
            raise ValueError("invalid thread id")
        elif res != 1:
            pythonapi.PyThreadState_SetAsyncExc(tid, None)
            raise SystemError("PyThreadState_SetAsyncExc failed")

        self._started.clear()

    def run(self):
        try:
            if self._target:
                self._target(*self._args, **self._kwargs)
        finally:
            self._started.clear()


```

一般不建议使用，可能会导致内存泄漏

