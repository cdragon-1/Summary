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
---
class SongSearchView(views.APIView):
    def get(self, request, query, format=None):
        return Response(['Justin Bieber - Boyfriend', 'Justin Timberlake - My Love'])

---
