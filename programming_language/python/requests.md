# Requests

```text
pip install requests
```

## Passing Parameters In URLs

```text
payload = {'key1': 'value1', 'key2': 'value2'}
r = requests.get("http://httpbin.org/get", params=payload)
print(r.url)
http://httpbin.org/get?key2=value2&key1=value1
```

## JSON Response Content

```text
import requests
r = requests.get('https://api.github.com/events')
r.json()
[{u'repository': {u'open_issues': 0, u'url': 'https://github.com/...
```

## Custom Headers

```text
import json
url = 'https://api.github.com/some/endpoint'
headers = {'user-agent': 'my-app/0.0.1'}
r = requests.get(url, headers=headers)
```

## More complicated POST requests

```text
import json
url = 'https://api.github.com/some/endpoint'
payload = {'some': 'data'}
r = requests.post(url, data=json.dumps(payload))
```

## Response Status Codes

We can check the response status code:

```text
r = requests.get('http://httpbin.org/get')
r.status_code
200
```

Requests also comes with a built-in status code lookup object for easy reference:

```text
r.status_code == requests.codes.ok
True
```

