### Locust


### 常用命令
```
pip3 install locust

locust -V
locust -h

locust -f locustfile.py
locust -f locustfile.py -U 2 -r 2 -t 10s --headless

locust -f locustfile.py --master
locust -f locustfile.py --worker

locust -f locustfile.py --master --expect-worker 2
locust -f locustfile.py --worker
locust -f locustfile.py --worker


docker run -p 8089:8089 -v $PWD:/mnt/locust -d locustio/locust -f /mnt/locust/locustfile.py
docker run -p 8089:8089 -v $PWD:/mnt/locust -d locustio/locust -f /mnt/locust/locustfile.py --html /mnt/locust/myrun.html --headless --only-summary -r 1 -u 1 -t 10s
```

### locustfile.py
```python
from locust import FastHttpUser, task

class QuickstartUser(FastHttpUser):
    @task
    def hello_world(self):
        self.client.get("/s?wd=test")
```

<br>
<br>

### 相关拓展
- [Locust Documentation](https://docs.locust.io/en/stable/)
- [Locust](https://locust.io/)

<br><br>