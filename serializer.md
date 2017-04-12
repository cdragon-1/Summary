##### serializer and deserializer

```python
>>> snippet = Snippet(code='print "hello, world"\n')
>>> snippet.save()
>>> serializer = SnippetSerializer(snippet)
>>> serializer.data
{'id': 2, 'language': 'python', 'linenos': False, 'style': 'friendly', 'title': '', 'code': 'print "hello, world"\n'}
>>> content = JSONRenderer().render(serializer.data)
>>> content
b'{"id":2,"title":"","code":"print \\"hello, world\\"\\n","linenos":false,"language":"python","style":"friendly"}'
>>> from django.utils.six import BytesIO
>>> stream = BytesIO(content)
>>> data = JSONParser().parse(stream)
>>> data
{'id': 2, 'language': 'python', 'linenos': False, 'style': 'friendly', 'title': '', 'code': 'print "hello, world"\n'}
```
