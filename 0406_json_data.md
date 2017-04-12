import urllib, json
url = "http://maps.googleapis.com/maps/api/geocode/json?address=google"
response = urllib.urlopen(url)
data = json.loads(response.read())
print data
output
{
"results" : [
    {
    "address_components" : [
        {
            "long_name" : "Charleston and Huff",
            "short_name" : "Charleston and Huff",
            "types" : [ "establishment", "point_of_interest" ]
        },
        {
            "long_name" : "Mountain View",
            "short_name" : "Mountain View",
            "types" : [ "locality", "political" ]
        },
        {
...

```python
jsonurl = urlopen(url)
text = json.loads(jsonurl.read()) # <-- read from it
import requests
r = requests.get('someurl')
print r.json()


url = "https://apis.deezer.com/2.0/search?q=mraz"
response = urllib.urlopen(url)
data = json.loads(response.read())
pprint('data: {}'.format(data))


class CommentList(generics.ListAPIView):    
    serializer_class = CommentSerializer
    permission_classes = (permissions.IsAuthenticatedOrReadOnly,)
    lookup_url_kwarg = "uid"

    def get_queryset(self):
        uid = self.kwargs.get(self.lookup_url_kwarg)
        comments = Comment.objects.filter(article=uid)
        return comments

        class PassengerList(generics.ListCreateAPIView):
            model = Passenger
            serializer_class = PassengerSerializer

            # Show all of the PASSENGERS in particular WORKSPACE
            # or all of the PASSENGERS in particular AIRLINE
            def get_queryset(self):
                queryset = Passenger.objects.all()
                workspace = self.request.query_params.get('workspace')
                airline = self.request.query_params.get('airline')

                if workspace:
                    queryset = queryset.filter(workspace_id=workspace)
                elif airline:
                    queryset = queryset.filter(workspace__airline_id=airline)

                return queryset

                from rest_framework import (
                    viewsets,
                    filters,
                )

                from .models import Player, Team
                from .serializers import PlayerSerializer, TeamSerializer
                from .pagination import ResultSetPagination
                from .validations import teams_query_schema, players_query_schema
                from filters.mixins import (
                    FiltersMixin,
                )


class PlayersViewSet(FiltersMixin, viewsets.ModelViewSet):
    """
    This viewset automatically provides `list`, `create`, `retrieve`,
    `update` and `destroy` actions.
    """
    serializer_class = PlayerSerializer
    pagination_class = ResultSetPagination
    filter_backends = (filters.OrderingFilter,)
    ordering_fields = ('id', 'name', 'update_ts')
    ordering = ('id',)

    # add a mapping of query_params to db_columns(queries)
    filter_mappings = {
        'id': 'id',
        'name': 'name__icontains',
        'team_id': 'teams',   # many-to-many relationship
        'install_ts': 'install_ts',
        'update_ts': 'update_ts',
        'update_ts__gte': 'update_ts__gte',
        'update_ts__lte': 'update_ts__lte',
    }

    # add validation on filters
    filter_validation_schema = players_query_schema

    def get_queryset(self):
        """
        Optionally restricts the queryset by filtering against
        query parameters in the URL.
        """
        query_params = self.request.query_params
        queryset = Player.objects.prefetch_related(
            'teams'  # use prefetch_related to minimize db hits.
        ).all()

        # This dict will hold filter kwargs to pass in to Django ORM calls.
        db_filters = {}

        # update filters dict with incoming query params and then pass as
        # **kwargs to queryset.filter()
         db_filters.update(
            self.get_queryset_filters(
                query_params
            )
        )
        return queryset.filter(**db_filters)


class MyQueryParamsExpectations(serializers.Serializer)
    count = fields.IntegerField()

class MyView(APIView):
    def get(request):
        qpe = MyQueryParamsExpectations(data=request.QUERY_PARAMS)
        if not qpe.is_valid():
            return Response(
                data=qpe.errors,
                status=status.HTTP_400_BAD_REQUEST
            )
        qp = qpe.object

        # do your work and be happy with less code :)


from rest_framework.views import APIView

class YourView(APIView):


  def get(self,request):
      parameters = request.query_params



      def search_from_deezer(keyword, page_token=None):
          deezer_api_key = config['deezer']['API_KEY_DEEZER']
          params = {
              'q': keyword,
              'maxResults': 30,
              'type': 'artist',
              'key': deezer_api_key,
          }

          r = requests.get('https://apis.deezer.com/2.0/search?q=', params=params)
          result = r.text

          # 해당 내용을 다시 json.loads()를 이용해 파이썬 객체로 변환
          result_dict = json.loads(result)
          return result_dict


      def search(request):
          musics = []
          context = {
              'musics': musics,
          }

          keyword = request.GET.get('keyword', '').strip()

          if keyword != '':
              search_result = search_from_deezer(keyword)

              context['keyword'] = keyword

              items = search_result['data']
              pprint(items)
              for item in items:
                  # 실제로 사용할 데이터
                  artist = item['artist']['name']
                  title = item['title']
                  preview = item['preview']
                  artist_picture = item['artist']['picture_small']
                  album_picture = item['album']['cover_small']

                  cur_item_dict = {
                      'artist': artist,
                      'title': title,
                      'preview': preview,
                      'artist_picture': artist_picture,
                      'album_picture': album_picture,
                  }
                  musics.append(cur_item_dict)
                  pprint(musics)

          return render(request, 'music/search.html', context)
```python


config_file = open(.conf-secret, settings_local.json).read()
config = json.loads(config_file)


DEBUG:True
DB_RDS:True
CONF_DIR:/home/pt/team-project/.conf-secret
CONFIG_FILE_NAME:settings_local.json
config_file:{
  "deezer": {
    "API_KEY_DEEZER": "05591e461b17b8cbdbb3aa9e55cd9b59"
  },
  "django": {
    "allowed_hosts": [
      "*"
    ],
    "secret_key": "+8acju5-&b39l)2m(s9zk7vvo*9%oy2#cr8&1)e)eu8!f32(!n"
  },
  "db": {
    "engine": "django.db.backends.postgresql_psycopg2",
    "name": "music_data_local",
    "user": "cyi",
    "password": "3506",
    "host": "localhost",
    "port": "5432"
  },
  "db_rds": {
    "engine": "django.db.backends.postgresql_psycopg2",
    "name": "music_data",
    "user": "cyi",
    "password": "ocho1253",
    "host": "musicdatainstance.c9chpniabz2m.ap-northeast-2.rds.amazonaws.com",
    "port": "5432"
  }
}

("config:{'db': {'host': 'localhost', 'port': '5432', 'user': 'cyi', "
 "'password': '3506', 'engine': 'django.db.backends.postgresql_psycopg2', "
 "'name': 'music_data_local'}, 'deezer': {'API_KEY_DEEZER': "
 "'05591e461b17b8cbdbb3aa9e55cd9b59'}, 'django': {'allowed_hosts': ['*'], "
 "'secret_key': '+8acju5-&b39l)2m(s9zk7vvo*9%oy2#cr8&1)e)eu8!f32(!n'}, "
 "'db_rds': {'host': "
 "'musicdatainstance.c9chpniabz2m.ap-northeast-2.rds.amazonaws.com', 'port': "
 "'5432', 'user': 'cyi', 'password': 'ocho1253', 'engine': "
 "'django.db.backends.postgresql_psycopg2', 'name': 'music_data'}}")
Performing system checks...




import requests

class IngestThirdPartyView():
    def post(self, request):
        data = request.data
        url = 'https://third_party.com/media/{}'.format(data['item_id]')
        item = requests.get(url).json() # this will retrieve json response
        # then you could deserialize the above json and validate it or something
        # as described here in docs http://www.django-rest-framework.org/api-guide/serializers/#deserializing-objects
        # Handle item somehow


        import httplib2 as http
        import json

        try:
            from urlparse import urlparse
        except ImportError:
            from urllib.parse import urlparse

        headers = {
            'Accept': 'application/json',
            'Content-Type': 'application/json; charset=UTF-8'
        }

        uri = 'http://yourservice.com'
        path = '/path/to/resource/'

        target = urlparse(uri+path)
        method = 'GET'
        body = ''

        h = http.Http()

        # If you need authentication some example:
        if auth:
            h.add_credentials(auth.user, auth.password)

        response, content = h.request(
                target.geturl(),
                method,
                body,
                headers)

        # assume that content is a json reply
        # parse content with the json module
        data = json.loads(content)



        params = {
        'USER' : 'xxxxxxxx', # Edit this to your API user name
        'PWD' : 'xxxxxxxx', # Edit this to your API password
        'SIGNATURE' : 'AFcWxV21C7fd0v3bYYYRCpSSRl31A0ltbCXAvF44j6B.kUqG3MePFr40',
        'METHOD':'SetExpressCheckout',
        'VERSION':86,
        'PAYMENTREQUEST_0_PAYMENTACTION':'SALE',     # type of payment
        'PAYMENTREQUEST_0_AMT':50,              # amount of transaction
        'PAYMENTREQUEST_0_CURRENCYCODE':'USD',   
        'cancelUrl':"xxxxxxxxxxxxx",    #For use if the consumer decides not to proceed with payment
        'returnUrl':"xxxxxxxxxxxx"   #For use if the consumer proceeds with payment
        }

            params_string = urllib.urlencode(params)
            response = urllib.urlopen('https://api-3t.sandbox.paypal.com/nvp', params_string).read() #gets the response and parse it.
            response_dict = parse_qs(response)
            response_token = response_dict['TOKEN'][0]
            rurl = PAYPAL_URL+response_token #gather the response token and redirect to paypal to authorize the payment
            return HttpResponseRedirect(rurl)

            params_string = urllib.urlencode(exparams)
             response = urllib.urlopen('https://api-3t.sandbox.paypal.com/nvp', params_string).read()
             return HttpResponse(response)


             params_string = urllib.urlencode(params)
   response = urllib.urlopen('https://api-3t.sandbox.paypal.com/nvp', params_string).read()
   response_dict = parse_qs(response)
   response_token = response_dict['TOKEN'][0]
   rurl = PAYPAL_URL+response_token
   return HttpResponseRedirect(rurl)



   request = httpclient.HTTPRequest(endpoint, body=json.dumps(data), method="POST", headers={"content-type": "application/json"})
   response = yield http_client.fetch(request)
   print response
