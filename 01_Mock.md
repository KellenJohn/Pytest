消化中
* [Python Mock的入門學習](https://www.itread01.com/content/1543845662.html)
* [Python 中 Mock 到底該怎麼玩？](https://www.gushiciku.cn/pl/gWIy/zh-tw)
* http://puremonkey2010.blogspot.com/2017/08/python-mock-python-unit-test.html
* https://kknews.cc/zh-tw/code/oq6nbb6.html
* [Pytest 大全](https://www.cnblogs.com/chenxiaomeng/category/1954939.html)
* https://pypi.org/project/pytest-mock/
其他
* [[Python] 在 pytest 裡 monkey patch 系統內建的函式](https://ephrain.net/python-%E5%9C%A8-pytest-%E8%A3%A1-monkey-patch-%E7%B3%BB%E7%B5%B1%E5%85%A7%E5%BB%BA%E7%9A%84%E5%87%BD%E5%BC%8F/)
* https://ajing-notebook.blogspot.com/2020/01/python-patch.html


### Mock module
在Python 3.3 以前的版本中，需要另外安裝 mock 模組，可以使用 pip 命令來安裝：
```sh
$ pip install mock
```
然後在程式碼中就可以直接import進來：
```python
import mock
```

從 Python 3.3 開始，mock 模組已經被合併到標準庫中，被命名為 unittest.mock，可以直接 import 進來使用：
```python
from unittest import mock
```

#### 基本用法
Mock 物件是 mock 模組中最重要的概念。Mock 物件就是 mock 模組中的一個類的例項，這個類的例項可以用來替換其他的 Python 物件，來達到模擬的效果。Mock 類的定義如下：</br>

📝 memo
```python
class Mock(spec=None, side_effect=None, return_value=DEFAULT, wraps=None, name=None, spec_set=None, **kwargs)
```

這裡給出這個定義只是要說明下 Mock 物件其實就是個 Python 類而已，當然，它內部的實現是很巧妙的，有興趣的可以去看 mock 模組的程式碼。</br>

mock 主要有 `name`, `return_value`, `side_effect` 和 `spec` 四個函式。</br>

主要的 assert 方法：</br>
* assert_called_with  是否呼叫了這個函式</br>
* assert_called_once_with  是否只調用了一次這個函式</br>
* assert_any_calls  是否呼叫了這個函式，前兩個函式只能判斷離它們最近的一次呼叫，這個是全域性的。</br>


Mock 物件的一般用法是這樣的：</br>
找到你要替換的物件，這個物件可以是一個類，或者是一個函式，或者是一個類例項。</br>
然後例項化 Mock 類得到一個 mock 物件，並且設定這個 mock 物件的行為，比如被呼叫的時候返回什麼值，被訪問成員的時候返回什麼值等。</br>
使用這個 mock 物件替換掉我們想替換的物件，也就是步驟1中確定的物件。</br>
之後就可以開始寫測試程式碼，這個時候我們可以保證我們替換掉的物件在測試用例執行的過程中行為和我們預設的一樣。</br>

---

### mock, unittest mock, pytest.mock 不同 module 說明
#### 1.mock

假設 Product 類中有 2 個方法</br>
* get_product_status_by_id - 方法還沒有實現
* buy_product - 方法依賴於 get_product_status_by_id 方法的返回值

📓 Example - product_impl.py
```python
class Product(object):

    def __init__(self):
        pass

    def get_product_status_by_id(self, product_id):
        """
        通過商品id獲取產品資訊（Mock）
        :return:
        """
        # 待實現查詢資料庫的業務邏輯
        pass

    def buy_product(self, product_id):
        """
        購買產品（真實邏輯）
        :return:
        """
        # 產品資訊
        # {"id":1,"name":"蘋果","num":23}
        product = self.get_product_status_by_id(product_id)

        if product.get("num") >= 1:
            result = {"status": 0, "msg": "購買成功！"}
        else:
            result = {"status": 1, "msg": "購買失敗，庫存不足！"}

        return result
```

📝 摘要 </br>
匯入使用 mock 中的 patch 方法 </br>
作為測試方法的裝飾器，對 `get_product_status_by_id` 方法進行 Mock，方法引數為 Mock 物件</br>
測試方法中，對該 Mock 物件設定一個返回值</br>
呼叫並斷言</br>

📓 Example
```python
from mock import patch
from mock_.product_impl import Product


@patch('mock_.product_impl.Product.get_product_status_by_id')
def test_succuse(mock_get_product_status_by_id):
  # Mock方法，指定一個返回值
  mock_get_product_status_by_id.return_value = {"id": 1, "name": "蘋果", "num": 23}
  product = Product()
  assert product.buy_product(1).get("status") == 0
```

📝 摘要</br>
需要注意的是，Mock 此方法的時候，必須制定該方法的完整路徑</br>
使用 @patch.object 同樣能完成 Mock，不同的是，@patch.object 包含 2 個引數</br>
第一個引數為該方法所在的類；第二個引數為方法名</br>

📓 Example
```python
from mock import patch
from mock_.product_impl import Product


# Mock一個方法
# @patch.object：物件、方法名
@patch.object(Product, 'get_product_status_by_id')
def test_succuse(mock_get_product_status_by_id):
  # Mock方法，指定一個返回值
  mock_get_product_status_by_id.return_value = {"id": 1, "name": "蘋果", "num": 23}
  product = Product()
  assert product.buy_product(1).get("status") == 0
 ```

📝 摘要 - mock_open
程式裡如果有對檔案做操作，免不了會直接用到 open() builtin，mock 很貼心地提供了 mock_open() helper：</br>
```python
mock_open(mock=None, read_data='')
```

其中 mock 可以提供自訂的 mock object (否則內部自動生出一個 MagicMock)，然後內部會將它 config 成像 open() 一樣，</br>
被呼叫時會傳回 file-like object，read_data 則可以進一步設定 file-like object 的 read() 要傳回什麼內容。用起來像是這樣子：</br>

```python
>>> from mock import mock_open
>>> open = mock_open(read_data='content') # 2
>>> f = open('filename', 'rb')            # 1
>>> f.read()                              # 2
'content'
>>> f.read()
'content'
>>>
>>> with open('filename', 'rb') as f:     # 3
...     print f.read()
...
content
```

* mock object 被呼叫時會傳回 file-like 的 mock object。
* 之後呼叫 file-like 的 read() 固定會傳回 read_data 參數傳入的內容。
* mock_open() 也支援 context manager 的用法。

#### 2.unittest mock
📝 摘要
* 匯入 unittest 框架中的 mock 檔案
* 例項化 Product 物件
* mock.Mock(return_value=*) 方法
* 對 get_product_status_by_id 方法進行 Mock
* 呼叫並斷言

📓 Example
```python
import unittest
from unittest import mock
from unittest_mock.product_impl import Product


class TestProduct(unittest.TestCase):

    def test_success(self):
        # 成功結果
        mock_success_value = {"id": 1, "name": "蘋果", "num": 23}

        product = Product()

        product.get_product_status_by_id = mock.Mock(return_value=mock_success_value)

        # 呼叫實際函式
        assert product.buy_product(1).get("status") == 0

if __name__ == "__main__":
    unittest.main()       
```
#### 3.pytest.mock
pytest-mock 是一個 pytest 的插件，安裝即可使用。 它提供了一個名為 mocker 的 fixture，僅在當前測試 function 或 method 生效，而不用自行包裝。</br>
如果專案本身使用的框架是 pytest，則 Mock 更建議使用 pytest-mock 這個外掛</br>
安裝：`pip install pytest-mock`</br>

##### (1)mocker.patch
mock 一個 object，是最常見的需求，由於 function 也是一個 object，以下以 function 舉例。</br>

📝 程式摘要
Mock 步驟如下：</br>
* 使用 pytest 編寫測試方法，引數為 `mocker`
* 例項化 Product 物件
* 使用 mocker.patch() 方法對 get_product_status_by_id 方法進行 Mock，並設定返回值
* 需要注意的是，`mocker.patch` 方法第一個引數必須是 Mock 物件的完整路徑
* 呼叫並斷言

📓 Example
```python
import pytest
from pytest_mock_.product_impl import Product


def test_buy_product_success(mocker):
    """
    購買成功Mock
    :param mocker:
    :return:
    """
    # 例項化一個產品物件
    product = Product()

    # 對Product中的方法的返回值進行Mock
    mock_value = {"id": 1, "name": "蘋果", "num": 23}

    # Mock方法
    # 注意：需要指定方法的完整路徑
    # mocker.patch 的第一個引數必須是模擬物件的『具體路徑』，第二個引數用來指定『返回值』
    product.get_product_status_by_id = mocker.patch("product_impl.Product.get_product_status_by_id",
                                                    return_value=mock_value)

    # 呼叫購買產品的方法
    result = product.buy_product(1)
    assert result.get("status") == 0
```


先將需要模擬的天氣接口，以及需要模擬的場景的代碼寫好，然後在進行遵循 pytest 的用例規範進行書寫關於 mock 的測試用例。</br>
📓 Example weateher_r.py
```python
class Mock_weather():
  def weather(self):
    '''天氣接口'''
    pass


  def weather_result(self):
    '''模擬天氣接口'''
    result = self.weather()
    if result['result'] == '雪':
      print('下雪了！！！')
    elif result['result'] == '雨':
      print('下雨了！！！')
    elif result['result'] == '晴天':
      print('晴天！！！！')
    else:
      print('返回值錯誤！')
  
    return result['status']
```

📓 Example - test_01.py
```python
import pytest
from test_01.weather_r import Mock_weather

def test_01(mocker):
  # 實例化
  p = Mock_weather()
  moke_value = {'result': "雪", 'status': '下雪了！'}
  # 通過object的方式進行查找需要mock的對象
  p.weather = mocker.patch.object(Mock_weather, "weather", return_value=moke_value)
  result =p.weather_result()
  assert result=='下雪了！'


def test_02(mocker):
  # 實例化
  product = Mock_weather()
  # Mock的返回值
  mock_value = {'result': "雨", 'status': '下雨了！'}
  # 第一個參數必須是模擬mock對象的完整路徑
  product.weather = mocker.patch('test_01.weather_r.Mock_weather.weather', return_value=mock_value)
  result = product.weather_result()
  assert result=='下雨了！'

if __name__ == '__main__':
  pytest.main(['-vs'])
```


📓 Example
```python
import os 


def rm(filename): 
  os.remove(filename) 
 
def test_rm(mocker):
  filename = 'test.file' 
  mocker.patch('os.remove') 
  rm(filename) 
  os.remove.assert_called_once_with(filename) 
```

這裡給 os.remove 打了一個 patch，讓它變成了一個 `MagicMock`。 然后利用 `assert_called_once_with`，查看它是否被調用一次，並且參數為 filename。</br>
注意：只能對已經存在的東西使用 mock。</br>


##### (2)mocker.patch.object
有時，僅僅需要 mock 一個 object 里的 method，而無需 mock 整個 object。 例如，在對當前 object 的某個 method 進行測試時。 這時，可以用 patch.object。

📓 Example
```python
class ForTest: 
  field = 'origin' 
  
  def method(): 
    pass 
  
def test_for_test(mocker): 
  test = ForTest() 
  mock_method = mocker.patch.object(test, 'method') 
  test.method() 
  assert mock_method.called
    
  assert 'origin' == test.field
  mocker.patch.object(test, 'field', 'mocked')
  assert 'mocked' == test.field
```
這裡的 mock 和 unittest 的 mock 基本上都是一樣的，唯一的區別在於 pytest.mock 需要導入需要 mock 對象的詳細路徑。</br>


通過上述代碼，提供pytest中mock的2種方法：</br>
第一種中的第一個參數是通過 `object` 的方式進行查找關於 `Mock_weather` 的類，然後在找到下面的需要 `mock` 的對象方法名稱，第2個參數表示 `mock` 的值。</br>
第二種方法中的第一個參數是通過完整的路徑進行找到需要 `mock` 的對象，第2個參數是 `mock` 的值。</br>
通過執行發現，兩種方法都是可以 `mock` 成功的。</br>
原文網址：https://kknews.cc/code/oq6nbb6.html</br>
