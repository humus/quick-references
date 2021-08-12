# Django Rest Framework quick reference

## Configurations

```
INSTALLED_APPS = [
...
    'rest_framework',
    'rest_framework_simplejwt',
...
]
REST_FRAMEWORK = {
    'DEFAULT_PERMISSION_CLASSES': ('rest_framework.permissions.IsAuthenticated',),
    'DEFAULT_AUTHENTICATION_CLASSES': (
        'rest_framework_simplejwt.authentication.JWTAuthentication',
    ),
}

DATABASES {
    'default': {
        'ENGINE': 'django.db.backends.postgresql_psycopg2',
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
from rest_framework import generics, status, viewsets
from rest_framework.response import Response
from rest_framework.permissions import IsAuthenticated

class SampleModelViewSet(viewsets.ModelViewSet):
    permissions_classes = [IsAuthenticated]
    queryset = Sample.objects.all() # use def get_queryset(self):... to customize
    serializer_class = SampleSerializer

# generics classes, self describing:

# generics.CreateAPIView
# generics.RetrieveUpdateAPIView
# generics.DestroyAPIView
# generics.RetrieveUpdateDestroyAPIView
# generics.GenericAPIView
# generics.UpdateAPIView
# generics.ValidationError
# generics.ListAPIView
# generics.api_settings
# generics.ListCreateAPIView
# generics.get_object_or_404
# generics.QuerySet
# generics.RetrieveAPIView
# generics.RetrieveDestroyAPIView

# rest_framework.permissions:
# AllowAny
# IsAdminUser
# IsAuthenticated
# IsAuthenticatedOrReadOnly

```

## Routing
```
from rest_framework.router import DefaultRouter
router = DefaultRouter()
router.register('{path_name}', SampleViewSet, basename='{path_name}')

urlpatterns += router.urls
```

## Serializers
```
from rest_framework import serializers
#assign user to field
created_by = serializers.HiddenField(default=serializers.CurrentUserDefault())
#assign current datetime to field
created_at = serializers.DateTimeField(default=serializers.CreateOnlyDefault(timezone.now))
# Example meta
    class Meta:
        model: Sample
        fields: ('id', 'created_at') # or use '__all__'
```
