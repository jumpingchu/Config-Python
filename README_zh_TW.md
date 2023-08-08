## 前言

在寫專案的時候，常會需要一些帳號密碼或是資料庫連線資訊，而這些資訊通常不建議直接寫在程式碼中，而是放在獨立的檔案中，這種檔案就叫做設定檔(Configuration Files)

但設定檔有很多種，像是 .json, .cfg, .ini, .yaml, .toml，撰寫的方式也有些許不同，用 Python 讀取的方式和套件也不同

所以這次整理了我目前工作上曾遇到的三種較常見的設定檔: .ini, .yaml, .toml，比較一下它們的優劣，並附上使用 Python 讀取它們的程式碼範例

## 比較

### ini
算是比較古早的一個設定檔類型

優點:
- 結構和寫法單純
- 有 section 做區分，不同的設定之間一目了然
- 使用 Python 很好讀取檔案
  
缺點:
- 無法撰寫巢狀縮排
- 無法撰寫註解
- 所有 value 讀出來都會變 String 字串型態

### YAML
原本全名是 Yet Another Markup Language
後來也有稱之為 YAML Ain't a Markup Language
用來強調 YAML 不是一個標記語言、不是文檔，而是利用做為數據
格式上算是 JSON 的進階版本，省去很多引號和括號，結構簡潔乾淨

優點:
- 清楚好讀的巢狀結構
- 會自動判別資料型態
- 可以寫註解

缺點:
- 沒有明顯的 section 做區分，設定資料多的時候會較混亂
- 太深的巢狀還是有可能會影響可讀性
- Python 目前無內建的套件可以讀取，需要另外安裝

### TOML

全名是 Tom's Obvious, Minimal Language
作者是 GitHub 創始人 Tom Preston-Werner
這算是近期比較新的設定檔，而且開始越來越泛用
Python 現在的設定檔也使用了 TOML (pyproject.toml) 
甚至 Python 3.11 版本開始
有一個叫做 tomllib 的套件也已經被納入內建套件了
看得出來 Python 越來越承認 TOML 這個設定檔了

優點:
- 清楚好讀的 section + 巢狀縮排結構 (也可選擇不要縮排)
- 可以寫註解
- 可以顯性指定資料型態 (就可以區分 bool 的 true 和 str 的 'true')
- 有內建的 Python 套件可以讀取檔案
 
缺點:
- 要定義資料型態在撰寫上就會比較麻煩
- 視覺上沒有 YAML 來的乾淨
- 有內建的套件是 Python 3.11 版本目前算是比較高

## 程式碼

### Read `.ini` file

```python
import configparser

def read_ini(filepath):
    config = configparser.ConfigParser()
    config.read(filepath)
    return config

ini_config = read_ini('config.ini')
mysql_info = ini_config['MYSQL']

host = mysql_info['HOST']
username = mysql_info['USERNAME'] 
port = mysql_info['PORT']
```

Data Types from **INI** file

> [!WARNING]
> 所有的值讀出來都會是 `String`，所以在使用 *port* 來連線資料庫之前，要記得先轉換成 `Int`

```
host: 127.0.0.1 (str)
username: mysql_root (str)
port: 3306 (str)
```

### Read `.yaml` file

> [!NOTE]
> 不要使用 `load`，要使用 `safe_load` 才能避免讀取 YAML 時不會執行參雜在檔案內的 Python 程式碼
> ([Python difference between yaml.load and yaml.safe_load](https://stackoverflow.com/questions/63911610/python-difference-between-yaml-load-and-yaml-safe-load))

```python
import yaml

def read_yaml(filepath):
    with open(filepath) as f:
        config = yaml.safe_load(f)
    return config

yaml_config = read_yaml('config.yaml')
mysql_info = yaml_config['DB_INFO']['MYSQL']

host = mysql_info['HOST']
username = mysql_info['USERNAME'] 
port = mysql_info['PORT']
```

Data Types from **YAML** file

```
host: 127.0.0.1 (str)
username: mysql_root (str)
port: 3306 (int)
```

### Read `.toml` file

> [!NOTE]
> 讀取 TOML 檔案要使用 `rb` 模式.

```py
import tomllib

def read_toml(filepath):
    with open(filepath, "rb") as f:
        config = tomllib.load(f)
    return config

toml_config = read_toml('config.toml')
mysql_info = toml_config['DB_INFO']['MYSQL']

host = mysql_info['HOST']
username = mysql_info['USERNAME'] 
port = mysql_info['PORT']
```

Data Types from **TOML** file

```
host: 127.0.0.1 (str)
username: mysql_root (str)
port: 3306 (int)
```

## 總比較表

|                     |     .ini     | .yaml  |        .toml |
| :------------------ | :----------: | :----: | -----------: |
| Read file by python |     Easy     |        |              |
| Python library      | configparser | pyyaml |      tomllib |
| Built-in library    |     Yes      |        | Python 3.11+ |
| Read config         |     Easy     |  Easy  |         Easy |
| Write config        |              |  Easy  |         Easy |
| Readability         |              |  High  |         High |
| Data Type           |    String    |  Mix   |          Mix |
| Write Comment       |              |  Yes   |          Yes |
| Case sensitive      |      No      |  Yes   |          Yes |

## 總結

比較過也實際使用過之後，我個人目前是對於 TOML 比較感興趣，因為我覺得 ini 的 section 很清楚，但 YAML 巢狀結構又比較好讀，而 TOML 算是結合了我想要的優點，所以之後我自己的專案可能會考慮多使用看看 TOML 來當主要設定檔。

當然這些設定檔之間其實沒有什麼真正的好壞之分，其實只要選擇自己習慣又看得順眼的就可以。

## 參考資料
- [JSON 就夠了，爲什麼還要 TOML 和 YAML？](https://medium.com/@WeZZard/json-%E5%B0%B1%E5%A4%A0%E4%BA%86-%E7%88%B2%E4%BB%80%E9%BA%BC%E9%82%84%E8%A6%81-toml-%E5%92%8C-yaml-9f0a288dcdbf)
- [pyproject.toml 介紹](https://blog.kyomind.tw/pyproject-toml/)
- [TOML 官網](https://toml.io/en/)
