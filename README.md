# Read Config Files by Python

There are many ways to read a configuration file by Python. In my experience, I have used 3 types of them in different jobs: `.ini`, `.yaml` and `.toml`.

Here are my opinions on these files and example code snippets for reading them using Python. 

## Table of content
- [Compare between different config files](#compare-between-different-config-files)
- [Example of Reading Configuration Files](#example-of-reading-configuration-files)
  - [Read `.ini` file](#read-ini-file)
  - [Read `.yaml` file](#read-yaml-file)
  - [Read `.toml` file](#read-toml-file)

## Compare between different config files

|                  |     .ini     | .yaml  |        .toml |
| :--------------- | :----------: | :----: | -----------: |
| Read file        |     Easy     |        |              |
| Python library   | configparser | pyyaml |      tomllib |
| Built-in library |     Yes      |        | Python 3.11+ |
| Read config      |     Easy     |  Easy  |         Easy |
| Write config     |              |  Easy  |         Easy |
| Readability      |     Low      |  High  |         High |
| Data Type        |    String    |  Mix   |          Mix |
| Write Comment    |              |  Yes   |          Yes |

## Example of Reading Configuration Files

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

#### Data Types from **INI** file

> [!WARNING]
> All config variables will be `String` type. You probably need to change the *port* to `Int` before creating DB connections.

```
host: 127.0.0.1 (str)
username: mysql_root (str)
port: 3306 (str)
```


### Read `.yaml` file

> [!NOTE]
> Use `safe_load` instead of `load` to avoid executing arbitrary Python code in YAML file. 
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

#### Data Types from **YAML** file

```
host: 127.0.0.1 (str)
username: mysql_root (str)
port: 3306 (int)
```

### Read `.toml` file

> [!NOTE]
> Open TOML file with mode `rb`.

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

#### Data Types from **TOML** file

```
host: 127.0.0.1 (str)
username: mysql_root (str)
port: 3306 (int)
```
