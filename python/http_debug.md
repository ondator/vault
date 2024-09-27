# HOWTO: Debug python HTTP requests
```python
import http.client as http_client
http_client.HTTPConnection.debuglevel = 1
import logging
logging.getLogger().setLevel(logging.DEBUG)
```

or instead of `logging.getLogger().setLevel(logging.DEBUG)` for airflow set env `export AIRFLOW__LOGGING__LOGGING_LEVEL=DEBUG`