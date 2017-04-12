##### httpie 사용하기!!!

pip install httpie
```json
http http://127.0.0.1:8000/snippets/

HTTP/1.1 200 OK
...
[
  {
    "id": 1,
    "title": "",
    "code": "foo = \"bar\"\n",
    "linenos": false,
    "language": "python",
    "style": "friendly"
  },
  {
    "id": 2,
    "title": "",
    "code": "print \"hello, world\"\n",
    "linenos": false,urlpatterns = format_suffix_patterns(urlpatterns)

    "language": "python",
    "style": "friendly"
  }
]
```
```python
http http://127.0.0.1:8000/snippets/ Accept:application/json  # Request JSON
http http://127.0.0.1:8000/snippets/ Accept:text/html         # Request HTML
# POST using form data
http --form POST http://127.0.0.1:8000/snippets/ code="print 123"
# POST using JSON
http --json POST http://127.0.0.1:8000/snippets/ code="print 456"
```
