---
description: Review best practices to deal with 5XX errors in Python
---

# 5XX Retry Logic Best Practices - Python

## Method 1

```python
import requests
from requests.adapters import HTTPAdapter
from requests.packages.urllib3.util.retry import Retry


def requests_retry_session(
    retries=3,
    backoff_factor=0.3,
    status_forcelist=(500, 502, 504),
    session=None,
):
    session = session or requests.Session()
    retry = Retry(
        total=retries,
        read=retries,
        connect=retries,
        backoff_factor=backoff_factor,
        status_forcelist=status_forcelist,
    )
    adapter = HTTPAdapter(max_retries=retry)
    session.mount('http://', adapter)
    session.mount('https://', adapter)
    return session
```

### Example

```python
response = requests_retry_session().get('https://www.peterbe.com/')
print(response.status_code)

s = requests.Session()
s.auth = ('user', 'pass')
s.headers.update({'x-test': 'true'})
response = requests_retry_session(session=s).get(
    'https://www.peterbe.com'
)
```

Get more details and examples [HERE](https://www.peterbe.com/plog/best-practice-with-retries-with-requests).

## **Method 2** 

Using pip install retry.

**Features**

* No external dependency \(stdlib only\).
* \(Optionally\) Preserve function signatures \(pip install decorator\).
* Original traceback, easy to debug.

**Installation**

```python
$ pip install retry
```

_API, retry decorator_

```python
def retry(exceptions=Exception, tries=-1, delay=0, max_delay=None, backoff=1, jitter=0, logger=logging_logger):
    """Return a retry decorator.

    :param exceptions: an exception or a tuple of exceptions to catch. default: Exception.
    :param tries: the maximum number of attempts. default: -1 (infinite).
    :param delay: initial delay between attempts. default: 0.
    :param max_delay: the maximum value of delay. default: None (no limit).
    :param backoff: multiplier applied to delay between attempts. default: 1 (no backoff).
    :param jitter: extra seconds added to delay between attempts. default: 0.
                   fixed if a number, random if a range tuple (min, max)
    :param logger: logger.warning(fmt, error, delay) will be called on failed attempts.
                   default: retry.logging_logger. if None, logging is disabled.
    """
```

  
****

  
**Various retrying logic can be achieved by combination of arguments. Check them** [**HERE**](https://pypi.org/project/retry/)  


