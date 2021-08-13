## Pytest
未消化
https://zhuanlan.zhihu.com/p/84138685
https://www.gushiciku.cn/pl/gCot/zh-tw
https://www.cnblogs.com/bainianminguo/p/13773717.html


單元測試
pytest 結合 Allure2 產生精美的測試報表
https://myapollo.com.tw/zh-tw/python-pytest-allure2-test-report-tool/

pytest 教學
https://myapollo.com.tw/zh-tw/pytest/

Python pytest 整合 coverage
https://myapollo.com.tw/zh-tw/pytest-coverage/
7 個實用的 pytest plugins
https://myapollo.com.tw/zh-tw/7-useful-pytest-plugins/

Python - 讓長字串排版變好看
https://myapollo.com.tw/zh-tw/python-prettify-long-string/
https://ppfocus.com/mo/0/diaa73ff9.html


https://blog.csdn.net/weixin_38374974/article/details/107245534
raises： 在断言一些代码块或者函数时会引发意料之中的异常或者其他失败的异常，导致程序无法运行时，使用 raises 捕获匹配到的异常，可以继续让代码正常运行。

预期内异常
```python
import pytest

def test_raises():
    with pytest.raises(ZeroDivisionError):
        2 / 0
    assert eval("1 + 2") == 3
```


如果我们不知道预期异常的是什么，我们可以使用 match 和 raise 进行自定义异常
```python
import pytest

def exc(x):
    if x == 0:
        raise ValueError("value not 0 or None")
    return 2 / x

def test_raises():
    with pytest.raises(ValueError, match="value not 0 or None"):
        exc(0)
    assert eval("1 + 2") == 3
```


参数化 pytest.mark.parametrize
使用参数化的话，可能会存在一部分用例可能会抛出异常，一部分可能会没有异常导致失败。如果想要其正常执行，则需要一个上下文管理器。这里官方指导使用 does_not_raise
例：
```python
from contextlib import contextmanager
import pytest

@contextmanager
def does_not_raise():
    yield

@pytest.mark.parametrize(
    "example_input,expectation",
    [
        (3, does_not_raise()),
        (2, does_not_raise()),
        (1, does_not_raise()),
        (0, pytest.raises(ZeroDivisionError)),
    ],
)
def test_division(example_input, expectation):
    """Test how much I know division."""
    with expectation:
        assert (6 / example_input) is not None
```

https://zhuanlan.zhihu.com/p/84138685
```python
@pytest.mark.skip(reason="no way of currently testing this")
def test_mark_skip():
    ...

def test_skip():
    if not valid_config():
        pytest.skip("unsupported configuration")

@pytest.mark.skipif(sys.version_info < (3, 6), reason="requires python3.6 or higher")
def test_mark_skip_if():
    ...

@pytest.mark.xfail
def test_mark_xfail():
    ...
```
