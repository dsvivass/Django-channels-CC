# Django-channels-CC

Tutorial link for more important concepts : https://www.geeksforgeeks.org/realtime-chat-app-using-django/

- Install Django Channels

```
    python -m pip install -U channels
```

- Add Django channels to INSTALLED_APPS

```
    INSTALLED_APPS = [
        ...
        'channels',
    ]
```

- Add Django channels to ASGI_APPLICATION

```
    ASGI_APPLICATION = 'ChatApp.asgi.application'
```

- Run the server

```
    python manage.py runserver
```

`Note: If a session storage error appears, see: https://stackoverflow.com/questions/27180353/django-project-looking-for-attribute-session-cache`

- Create a new app

```
    python manage.py startapp chat
```

- Add chat to INSTALLED_APPS

```
    INSTALLED_APPS = [
        ...
        'chat',
    ]
```

- Add urls and everything as always

- Add routing.py

```
    from django.urls import path , include
    from chat.consumers import ChatConsumer
    
    # Here, "" is routing to the URL ChatConsumer which
    # will handle the chat functionality.
    websocket_urlpatterns = [
        path("" , ChatConsumer.as_asgi()) ,
    ]
```

- in the asgi file, add the routing

```
    import os
    from django.core.asgi import get_asgi_application
    
    os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'ChatApp.settings')
    
    from channels.auth import AuthMiddlewareStack
    from channels.routing import ProtocolTypeRouter , URLRouter
    from chat import routing
    
    application = ProtocolTypeRouter(
        {
            "http" : get_asgi_application() ,
            "websocket" : AuthMiddlewareStack(
                URLRouter(
                    routing.websocket_urlpatterns
                )   
            )
        }
    )
```

- Channel layer
This code defines the channel layer in which we will be working and sharing data. For the deployment and production level, don’t use InMemoryChannelLayer, because there are huge chances for your data leakage. This is not good for production. For production use the Redis channel.

```
    # In settings.py
    CHANNEL_LAYERS = {
        "default" : {
            "BACKEND" : "channels.layers.InMemoryChannelLayer"
        }
    }
```

`Note: If you are using Redis, you need to install it first`

```
    pip install channels_redis
```