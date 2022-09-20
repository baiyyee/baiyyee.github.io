# WeTest

**WeTest** is a simple, yet useful, test toolkit.

WeTest allows you to start an automated testing project extremely easily, like api test, macro support, tracker requests, testdata build, common clients connection...

Just try a little test scenario:

---

**Scenario 01: Send Request** 


**testdata**: testdata.xlsx

| tc_id        | tc_desc    | method | headers                                                        | json | data | protocol | domain     | path     | ip               | accessKey   | plain_string | loop | expect_code | expect_response |
| ------------ | ---------- | ------ | -------------------------------------------------------------- | ---- | ---- | -------- | ---------- | -------- | ---------------- | ----------- | ------------ | ---- | ----------- | --------------- |
| ip_parse_001 | ipv4_parse | get    | user-agent > {{data.user_agent()}} \| referer > {{data.url()}} |      |      | https    | {{domain}} | {{path}} | {{ipv4.beijing}} | alibaba-inc |              |      | 200         | "city":"北京"   |

**testcase**: tests/tool/test_tracker.py

```python
import pytest
from WeTest.tool import tracker
from WeTest.util import provider


data = provider.read_excel_to_dict("testdata.xlsx", 0)

@pytest.mark.parametrize("data", data, ids=lambda data: "{}-{}".format(data["tc_id"], data["tc_desc"]))
def test_ipparse_ipv4(data):

    macro = [("{{domain}}", "ip.taobao.com"), ("{{path}}", "outGetIpInfo"), ("{{ipv4.beijing}}", "1.2.5.0")]

    tracker.send(macro, None, **data)

```

```console
$ pytest tests/tool/test_tracker.py::test_ipparse_ipv4

collecting ... 
-------------------------------------------------------------------------------------- live log call --------------------------------------------------------------------------------------
2022-08-20 15:38:17 [INFO] 
========================================= [ REQUEST ] =========================================
GET https://ip.taobao.com/outGetIpInfo?request_id=jp_parase_001_ipv4_parse_0001&ip=1.2.5.0&accessKey=alibaba-inc
user-agent: Mozilla/5.0 (Linux; Android 5.0) AppleWebKit/536.2 (KHTML, like Gecko) Chrome/63.0.850.0 Safari/536.2
Accept-Encoding: gzip, deflate, br
Accept: */*
Connection: keep-alive
referer: http://www.fanding.cn/

None

========================================= [ RESPONSE ] =========================================
200  https://ip.taobao.com/outGetIpInfo?request_id=jp_parase_001_ipv4_parse_0001&ip=1.2.5.0&accessKey=alibaba-inc
Date: Sat, 20 Aug 2022 07:38:12 GMT
Content-Type: application/json;charset=UTF-8
Transfer-Encoding: chunked
Connection: keep-alive
Vary: Accept-Encoding
Set-Cookie: XSRF-TOKEN=8b64ecea-e4db-41fe-8351-98f87e45b732; Path=/
X-Content-Type-Options: nosniff
X-XSS-Protection: 1; mode=block
Cache-Control: no-cache, no-store, max-age=0, must-revalidate
Pragma: no-cache
Expires: 0
X-Frame-Options: DENY
Content-Encoding: gzip
Server: Tengine/Aserver
EagleEye-TraceId: 213fc9b716609810926637035e8a94
Strict-Transport-Security: max-age=31536000
Timing-Allow-Origin: *

{"data":{"area":"","country":"中国","isp_id":"100017","queryIp":"1.2.5.0","city":"北京","ip":"1.2.5.0","isp":"电信","county":"","region_id":"110000","area_id":"","county_id":null,"region":"北京","country_id":"CN","city_id":"110100"},"msg":"query success","code":0}


 tests/tool/test_tracker.py::test_ipparse_ipv4[jp_parase_001-ipv4_parse] ✓                                                                                                  100% ██████████

Results (1.16s):
       1 passed
```

---
**Scenario 02: API Test** 


**testdata**: data/api/testdata_user.xlsx

| tc_id       | tc_desc          | name                            | email                           | role                            | status                         | password                       | expect_code | expect_response                                                  |
| ----------- | ---------------- | ------------------------------- | ------------------------------- | ------------------------------- | ------------------------------ | ------------------------------ | ----------- | ---------------------------------------------------------------- |
| tc_user_001 | 合法-快乐路径    | {{data.string(min=10, max=20)}} | {{data.email()}}                | {{data.random_choice([0,1,2])}} | 1                              | {{data.string(min=6, max=20)}} | 200         |
| tc_user_002 | 非法-用户名-空值 | {{data.email()}}                | {{data.random_choice([0,1,2])}} | 1                               | {{data.string(min=6, max=20)}} |                                | 422         | {"error":{"name":"ensure this value has at least 2 characters"}} |


**testcase**: test/api/test_user.py

```python
import os
import sys

sys.path.append(os.getcwd())

import json
import pytest
import allure
from config import apis
from WeTest.util.api import API
from config.const import TIMEOUT
from WeTest.util.client import DataBase
from WeTest.util import provider, compare


@allure.epic("User Management")
@pytest.mark.run(order=1)
@pytest.mark.p0
@pytest.mark.api
class TestUser:

    user = {}

    path = "data/api/testdata_user.xlsx"
    testdata_user_create = provider.read_excel_to_dict(path, "user")

    @allure.story("Create User")
    @pytest.mark.timeout(TIMEOUT)
    @pytest.mark.dependency(name="test_create_user")
    @pytest.mark.parametrize("testdata", testdata_user_create, ids=lambda data: "[{tc_id}: {tc_desc}]".format(**data))
    def test_create_user(self, api: API, testdata):
        """Verify Create User Success"""

        path = apis.USER

        _, kw = provider.replace_macro(None, None, **testdata)

        status_code = int(kw["expect_code"])

        payload = """
        {{
            "name": "{name}",
            "email": "{email}",
            "role": "{role}",
            "status": "{status}",
            "password": "{password}"
        }}
        """.format(
            **kw
        )

        payload = json.loads(payload)

        response = api.request("POST", path, json=payload)

        assert response.status_code == status_code

        response = response.json()

        if status_code == 200:
            self.user["id"] = response["id"]

            assert response["name"] == payload["name"]
            assert response["email"] == payload["email"]
            assert response["role"] == int(payload["role"])
            assert response["status"] == int(payload["status"])
            assert response["name"] == payload["name"]
        else:
            assert response == json.loads(kw["expect_response"])
```

```console
$ pytest test/api/test_user.py::TestUser::test_create_user
Test session starts (platform: linux, Python 3.8.0, pytest 7.1.2, pytest-sugar 0.9.5)
rootdir: /mnt/d/src/python/TestBase, configfile: pytest.ini
plugins: dependency-0.5.1, playwright-0.3.0, forked-1.4.0, rerunfailures-10.2, metadata-2.0.2, sugar-0.9.5, repeat-0.9.1, timeout-2.1.0, playwright-snapshot-1.0, ordering-0.6, xdist-2.5.0, allure-pytest-2.10.0, base-url-2.0.0, asyncio-0.19.0, html-3.1.1, anyio-3.6.1, Faker-14.1.0
asyncio: mode=strict
collecting ... 
------------------------------------------------------------------------------------------------ live log setup -------------------------------------------------------------------------------------------------
2022-09-11 18:42:11 [INFO] **************************************************
2022-09-11 18:42:11 [INFO]      >>>>> CURRENT ENVIRONMENT: DEV <<<<<
2022-09-11 18:42:11 [INFO] **************************************************
2022-09-11 18:42:12 [INFO] 
========================================= [ REQUEST ] =========================================
POST http://127.0.0.1:8000/login
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/80.0.3987.132 Safari/537.36
Accept-Encoding: gzip, deflate, br
Accept: */*
Connection: keep-alive
Content-Length: 78
Content-Type: application/x-www-form-urlencoded

client_id=&client_secret=&grant_type=&username=root%40test.com&password=123456

========================================= [ RESPONSE ] =========================================
200 OK http://127.0.0.1:8000/login
date: Sun, 11 Sep 2022 10:42:11 GMT
server: uvicorn
content-length: 192
content-type: application/json

{"token_type":"bearer","access_token":"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJyb290QHRlc3QuY29tIiwic2NvcGVzIjpbXSwiZXhwIjoxNjYyODk0NzMxfQ.oySUtuqiKS-3FRWyHhvB3Ip7g8PtZDu3bnKBvVZ7nB8"}

------------------------------------------------------------------------------------------------- live log call -------------------------------------------------------------------------------------------------
2022-09-11 18:42:12 [INFO] 
========================================= [ REQUEST ] =========================================
POST http://127.0.0.1:8000/user/
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/80.0.3987.132 Safari/537.36
Accept-Encoding: gzip, deflate, br
Accept: */*
Connection: keep-alive
Authorization: bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJyb290QHRlc3QuY29tIiwic2NvcGVzIjpbXSwiZXhwIjoxNjYyODk0NzMxfQ.oySUtuqiKS-3FRWyHhvB3Ip7g8PtZDu3bnKBvVZ7nB8
Content-Length: 123
Content-Type: application/json

b'{"name": "ipBvXEIlqs7fd6h6O1RZ", "email": "fangwen@example.org", "role": "0", "status": "1", "password": "oDFD9NTgoxoLwZW"}'

========================================= [ RESPONSE ] =========================================
200 OK http://127.0.0.1:8000/user/
date: Sun, 11 Sep 2022 10:42:11 GMT
server: uvicorn
content-length: 100
content-type: application/json

{"name":"ipBvXEIlqs7fd6h6O1RZ","email":"fangwen@example.org","role":0,"status":1,"id":2,"creator":1}


 test/api/test_user.py::TestUser.test_create_user[[root]-[DEV]-[tc_user_001: 合法-快乐路径]] ✓                                                                                                           50% █████     
------------------------------------------------------------------------------------------------- live log call -------------------------------------------------------------------------------------------------
2022-09-11 18:42:12 [INFO] 
========================================= [ REQUEST ] =========================================
POST http://127.0.0.1:8000/user/
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/80.0.3987.132 Safari/537.36
Accept-Encoding: gzip, deflate, br
Accept: */*
Connection: keep-alive
Authorization: bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJyb290QHRlc3QuY29tIiwic2NvcGVzIjpbXSwiZXhwIjoxNjYyODk0NzMxfQ.oySUtuqiKS-3FRWyHhvB3Ip7g8PtZDu3bnKBvVZ7nB8
Content-Length: 106
Content-Type: application/json

b'{"name": "", "email": "yang80@example.com", "role": "0", "status": "1", "password": "EyAsZvTMWvaITHBSYEc"}'

========================================= [ RESPONSE ] =========================================
422 Unprocessable Entity http://127.0.0.1:8000/user/
date: Sun, 11 Sep 2022 10:42:11 GMT
server: uvicorn
content-length: 64
content-type: application/json

{"error":{"name":"ensure this value has at least 2 characters"}}


 test/api/test_user.py::TestUser.test_create_user[[root]-[DEV]-[tc_user_002: 非法-用户名-空值]] ✓                                                                                                        100% ██████████

Results (1.84s):
       2 passed
```

## Installing WeTest and Supported Versions

WeTest is available on PyPI:

- For common use:

```console
$ python -m pip install WeTest
```

- For **Hive** support:
```console
$ python -m pip install "WeTest[hive]"
```

## Features

WeTest is ready for the demands of building automation test project, for the needs of today.

- util
  - algorithm
    - upstream_round_robin
  - api
    - request
    - upload
    - download
  - client
    - database
    - rabbitmq
    - sftp
    - ssh
    - nacos
    - s3
    - hive
  - compare
    - dataframe
    - dict
    - list
    - schema
    - file
    - image
  - config
    - yaml
    - ini
    - json
  - date
    - format
    - convert
    - datetime
    - timestamp
  - encry
    - aes
    - md5
    - url
    - sha1
    - base64
  - network
    - ip
    - url
    - useragent
    - file mime type
  - notication
    - work wechat
    - wechat(todo)
    - email(todo)
  - path
    - all path related scenario
  - provider
    - dataframe & excel
    - read & output
    - unpack dict
    - str to bool
    - replace macro
  - request
    - request by remote ip (in LB)
    - get romote ip
    - get redirect history
    - async request
    - bulk request
    - upload file(s)
    - download
    - log response
  - testdata
    - fake data


- tool
  - cookdata
    - cook df data
    - cook db data
  - decorator
    - timeit
  - logtime
    - timestamp
  - jira issue analytics
  - server(todo)
    - online api call
    - data visualization
  - tracker


## TestBase

Wanna to try? <a href="https://github.com/baiyyee/TestBase" target="_blank">**TestBase**</a> is a scaffold project and based on **WeTest**, it's ready for you to speed up your automated testing process.