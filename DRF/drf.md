# Django Rest Framework quick reference

## Configurations

```
INSTALLED\_APPS = [
...
    'rest\_framework',
    'rest\_framework\_simplejwt',
...
]
REST\_FRAMEWORK = {
    'DEFAULT\_PERMISSION\_CLASSES': ('rest\_framework.permissions.IsAuthenticated',),
    'DEFAULT\_AUTHENTICATION\_CLASSES': (
        'rest\_framework\_simplejwt.authentication.JWTAuthentication',
    ),
}

DATABASES {
    'default': {
        'ENGINE': 'django.db.backends.postgresql\_psycopg2',
        'NAME': '{dbname}',
        'USER': '{dbuser}',
        'PASSWORD': '{dbuserpassword}',
        'HOST': '{dbhost:localhost}',
        'PORT': '5432',
    }
}
```

## Generic views
```
from rest\_framework import generics, status, viewsets
from rest\_framework.response import Response
from rest\_framework.permissions import IsAuthenticated

class SampleModelViewSet(viewsets.ModelViewSet):
    permissions\_classes = [IsAuthenticated]
    queryset = Sample.objects.all() # use def get\_queryset(self):... to customize
    serializer\_class = SampleSerializer

# generics classes, self describing:

# generics.CreateAPIView
# generics.RetrieveUpdateAPIView
# generics.DestroyAPIView
# generics.RetrieveUpdateDestroyAPIView
# generics.GenericAPIView
# generics.UpdateAPIView
# generics.ValidationError
# generics.ListAPIView
# generics.api\_settings
# generics.ListCreateAPIView
# generics.get\_object\_or\_404
# generics.QuerySet
# generics.RetrieveAPIView
# generics.RetrieveDestroyAPIView

# rest\_framework.permissions:
# AllowAny
# IsAdminUser
# IsAuthenticated
# IsAuthenticatedOrReadOnly

```

## Routing
```
from rest\_framework.router import DefaultRouter
router = DefaultRouter()
router.register('{path\_name}', SampleViewSet, basename='{path\_name}')

urlpatterns += router.urls
```

## Serializers
```
from rest_framework import serializers
#assign user to field
created\_by = serializers.HiddenField(default=serializers.CurrentUserDefault())
#assign current datetime to field
created\_at = serializers.DateTimeField(default=serializers.CreateOnlyDefault(timezone.now))
# Example meta
    class Meta:
        model: Sample
        fields: ('id', 'created_at') # or use '__all__'
```
