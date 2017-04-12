#### pagination

settings.py
```python
REST_FRAMEWORK = {
    'PAGE_SIZE': 10
}
```

rest에서 pagination 기능 구현은 의외로 간단하였다. 위와 같이 적용하면 기본적인 숫자 형태의 페이지가 나오게 되고, 아래의 옵션을 찾아서 적용하면 cursor based pagination을 사용할 수 있게 된다.

limit/offset style,

Custom pagination styles
To create a custom pagination serializer class you should subclass pagination.BasePagination and override the paginate_queryset(self, queryset, request, view=None) and get_paginated_response(self, data) methods:

The paginate_queryset method is passed the initial queryset and should return an iterable object that contains only the data in the requested page.
The get_paginated_response method is passed the serialized page data and should return a Response instance.
Note that the paginate_queryset method may set state on the pagination instance, that may later be used by the get_paginated_response method.

- example
```python
class CustomPagination(pagination.PageNumberPagination):
    def get_paginated_response(self, data):
        return Response({
            'links': {
               'next': self.get_next_link(),
               'previous': self.get_previous_link()
            },
            'count': self.page.paginator.count,
            'results': data
        })

REST_FRAMEWORK = {
    'DEFAULT_PAGINATION_CLASS': 'my_project.apps.core.pagination.CustomPagination',
    'PAGE_SIZE': 100
}
```

\- 자료는 django REST_FRAMEWORK의 공식문서 참조함
http://www.django-rest-framework.org/api-guide/pagination/#custom-pagination-styles
