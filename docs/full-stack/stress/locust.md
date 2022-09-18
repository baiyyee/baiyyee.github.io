### Locust


### locustfile.py
```python
from locust import FastHttpUser, task

class QuickstartUser(FastHttpUser):
    @task
    def hello_world(self):
        self.client.get("/s?wd=test")
```

<br>


### 常用命令
```
locust -f locustfile.py
```

<br>

### 相关拓展
- [Locust Documentation](https://docs.locust.io/en/stable/)
- [Locust](https://locust.io/)

<br><br>