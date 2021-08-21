## Pytest
* [Python Table Manners - 測試 (一)](https://zh-tw.coderbridge.com/series/66cb226274ea4d349abd49d2aef44037/posts/b00ffb74e7534d018a84b98cad4a7498)
* [★Python Table Manners - 測試 (二)](https://zh-tw.coderbridge.com/series/66cb226274ea4d349abd49d2aef44037/posts/9fb680151c5e4bf38ab35eebd0a35c4b)
* [Wendy](https://hackmd.io/@esun-mlops/HkTZCsJOu)
未消化
* https://zhuanlan.zhihu.com/p/84138685
* https://www.gushiciku.cn/pl/gCot/zh-tw
* https://www.cnblogs.com/bainianminguo/p/13773717.html
* https://note.qidong.name/2019/01/pytest-httpserver/
* https://note.qidong.name/2018/02/pytest-mock/
★封神之路
* https://codingnote.cc/zh-tw/p/207951/

★IT人
https://iter01.com/596979.html
★dict
https://mmx362003.gitbooks.io/pytest-guide/content/chapter1/14-pytest-fixture-param-and-parametrize.html

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


### 資料夾結構
```python
  pytest_example
  ├── README.md
  ├── api
  │   ├── api.py
  │   ├── driver.log
  │   ├── my_log.py
  │   ├── testDB.db
  │   ├── testDB.sql
  │   └── utils.py
  ├── api_tests
  │   ├── conftest.py
  │   ├── test_api.py
  │   └── test_utils.py
  ├── functions
  │   └── basic.py
  ├── functions_tests
  │   ├── conftest.py
  │   └── test_basic.py
  ├── myclass
  │   ├── Person.py
  │   ├── __init__.py
  ├── myclass_test
  │   ├── __init__.py
  │   ├── conftest.py
  │   └── test_myclass.py
  ├── pytest.ini
  ├── .coveragerc
  └── requirements.txt
```

### 基本 Configuration files (1)：pytest.ini
* pytest initial 時會參考的配置檔，可以在裡面設定每次 pytest 要使用的設定，通常放置於 repo 的根目錄或測試目錄中。
* pytest 可使用的配置檔眾多，pytest.ini 文件會優先於其他文件，即使是空的。

#### pytest-cov 測試覆蓋率
> * --cov=[SOURCE]: 測試包含的程式碼範圍(如果我們想指定執行專案裡面的某個模)
>                   也可以指定具體的py模組名稱 pytest --cov=src.pay 但不能寫成pytest --cov=src/pay.py
> * --cov-report=TYPE: 測試覆蓋率報告的種類 (term, term-missing, annotate, html, xml)
> * --cov-fail-under=MIN: 如果覆蓋率小於 MIN 則跳出
其中 --cov, --cov-report 都可以加入多個參數


pytest.ini
擺放至 job 底下，在這的資料夾名稱使用如下
另外，terminal 下 pytest 因為 test_main.py 放在 tests 所以要加 pytest tests/test_main.py
```sh
[pytest]
addopts = -v -s
          --cov=tests
          --cov=server_info
          --cov-report=term-missing
          --cov-report=html
          --cov-config=tests/.coveragerc
          
testpaths = tests
python_files = test_*.py
python_classes = Test*
python_function = test_*


markers = 
    xlsreader:
    bauu:
```


例如
```sh
pipenv run pytest --cov=report_generator --cov-report=term-missing test/
```
從下面的結果可以看到哪些檔案的哪些部分沒有被測試到

![image](https://github.com/KellenJohn/Pytest/blob/main/pytest01.jpg)


如果想看精美的網頁版報告，可以試試看以下的指令
報告會產生在專案資料夾下的 htmlcov
```sh
pipenv run pytest --cov=report_generator --cov-report=term-missing --cov-report=html
```

#### 基本 Configuration files (2)：conftest.py
* pytest在執行任何一個單元測試的時候，最靠近執行目錄下的那個conftest.py將被自動執行。
* conftest.py 是 pytest 中的一個特殊檔案
* 如果是整個套件（同一個資料夾）都會用到的 fixture 就能放在這， pytest 執行時會自動載入

以下面的結構為例，<font color=#800000> `test_sponsor.py`</font> 就會自動載入上層的 conftest.py 中的 fixture

fixture 的 scope 共分為五種 （function, class, module, package, session）
表示 fixture 會在哪個階段前準備資源，並在哪個階段後清除
如果設定成 function，就會在每一個測試函式執行前和後做資源的處理

```python
└── tests
    ├── __init__.py
    ├── conftest.py
    ├── test_sponsor.py
    └── page
        ├── __init__.py
        ├── conftest.py
        └── test_title.py
```



### pytest 常用命令列參數
```sh
-v (-vv, -vvv): 顯示更多資訊 （越多 v 就會顯示越多資訊）
--durations=N: 只列出最慢的 N 個測試
-x (--exitfirst): 遇到第一個失敗就終止測試
--maxfail=num: 失敗次數達到 num 次，直接終止測試
--lf (--last-failed): 只測試上次失敗的案例
--ff (--failed-first): 從上次失敗的案例開始測試
--nf --new-first: 從新的案例開始測試
-k EXPRESSION: 只測試名稱符合 "EXPRESIION" 的案例
-m MARKEXPR: 只測試有 "MARKEXPR" maker 的案例
--fixtures: 列出所有 fixtures
```

`pytest` 也支持執行指定：

指定測試文件路徑
`pytest /path/to/test/file.py`
指定測試類
`pytest /path/to/test/file.py:TestCase`
指定測試方法
`pytest another.test::TestClass::test_method`
指定測試函數
`pytest /path/to/test/file.py:test_function`


#### 基本 Configuration files (3)：.coveragerc 設定檔
有時候有些程式其實不需要衡量覆蓋率，譬如 __init__.py 一般都會是空的，如果我們希望將它在報告中排除，就可以用 .coveragerc 設定檔進行排除，以下是 .coveragerc 的範例：
計算 coverage 的額外設定檔，可以設定計算時忽略的檔案以及不需測試的程式碼列表，可以放於根目錄或測試程式目錄並於 pytest.ini 指定位置

```python
[run]
omit = 
    # omit all files in this directory
    api/*
    */__init__.py
    # omit this single file
    src/tirefire.py

[report]
exclude_lines =
    if __name__ == .__main__.:
    except ImportError:
    pass    
```
---
### 測試例外事件
透過 pytest.raise 確認測試案例是否有符合預期的丟出例外事件
```python
import pytest


def test_index_error():
    some_list = []
    with pytest.raises(IndexError):
        print(some_list[1])
        
def test_type_error():
    x = "hello"
    with pytest.raises(TypeError):
        x + []
```
https://blog.csdn.net/weixin_38374974/article/details/107245534
raises： 在断言一些代码塊或者函數时会引发意料之中的异常或者其他失败的异常，导致程序无法运行时，使用 raises 捕获匹配到的异常，可以继续让代码正常运行。

预期内异常
```python
import pytest

def test_raises():
    with pytest.raises(ZeroDivisionError):
        2 / 0
    assert eval("1 + 2") == 3
```


如果我们不知道预期异常的是什么，我们可以使用 match 和 raise 进行自定義异常
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


參數化 pytest.mark.parametrize
使用參數化的话，可能会存在一部分用例可能会抛出异常，一部分可能会没有异常导致失败。如果想要其正常执行，則需要一個上下文管理器。这里官方指导使用 does_not_raise
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


#### 內建 fixture

pytest中，一個fixture其實就是一個函數，函數名就是fixture的名稱。關於fixture，pytest在run一個test方法的時候，大概流程如下：</br>

收集該test方法的作用域內的所有fixture； 這個作用域包括該test case所屬的class，module等不同作用域的疊加（遇到同名的fixture，更里層的會覆蓋更外層的）：</br>

> The discovery of fixtures functions starts at test classes, then test modules, then conftest.py files and finally builtin and third party plugins.

收集該test方法的pytest參數（通過pytest.mark.parametrize來定義）；</br>

對該test方法的輸入根據該作用域內的fixture名稱或pytest參數（遇到pytest參數和fixture同名的，pytest參數會覆蓋fixture）進行填充後執行。</br>

pytest中的fixture提供了一個很好的對象管理方式，我們可以將測試代碼中經常用到的一些對象定義為fixture來統一進行管理，而省去了很多重復的代碼（這點就像with-statement，不但減少了代碼量，而且避免了代碼中類似資源未正確釋放的情況）。</br>

##### 宣告和使用
pytest 中的fixture更像是測試資源，你只需定義一個 fixture，就可以在用例中直接使用它。得益于 pytest 的依賴注入機制，你無需 from xx import xx 導入，只需要在測試函數的參數中指定同名參數即可

用法: 他在每一個function重新建立一次
範例：我們定義了一個 smtp_connection 將他當成參數傳入到 test_ehlo function 內
```python
import pytest


@pytest.fixture
def smtp_connection():
    import smtplib

    return smtplib.SMTP("smtp.gmail.com", 587, timeout=5)


def test_ehlo(smtp_connection):
    response, msg = smtp_connection.ehlo()
    assert response == 250
```

##### 共享
同一個fixture可被多個測試文件中的多個測試用例共享。只需在包（Package）中定義 conftest.py 文件，并把fixture的定義寫在文件中，則包内所有模塊（Module）的所有測試用例均可使用 conftest.py 中所定義的fixture。如下文件的 test_1/conftest.py 定義了fixture，那 test_a.py 和 test_b.py 可以使用該fixture；而 test_c.py 則無 法使用。

```python
└── test_1
    ├──  conftest.py
    ├──  test_a.py
    └── test_b.py
└── test_2
    └── test_c.py
```

##### fixture params
@pytest.fixture 定義在fixture 內 透過params去定義
params 可以傳入object or string value

```python
@pytest.fixture(scope="function", params=["opt1", "opt2"])
def optmod(request):
    return request.param
def test_params(optmod):
    print(optmod)
# --------------------------

>>> test_fixture.py::test_params[opt1] opt1
PASSED
>>> test_fixture.py::test_params[opt2] opt2
PASSED

```



### marker

#### 參數化 (parameterize)
在測試資料比較簡單的時候，可以使用 parameterize 來減少撰寫重複的程式碼
```python
@pytest.mark.parametrize(args1, arg2)
第一個參數: 指定測試函式要使用的參數名稱
第二個參數: 測試資料的陣列
import pytest


@pytest.mark.parametrize(
    "x, y, expected_sum",
    (
        (1, 1, 2),
        (2, 2, 4),
        (3, 3, 6),
    ),
)
def test_add(x, y, expected_sum):
    assert x + y == expected_sum
```





* skip: 跳過這個測試案例
* skipif: 如果符合某個條件，則跳過這個測試案例
* xfail: 預期會失敗 （其實前一篇想跳過會失敗的案例應該要用 xfail，而不是 skip）

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
