## rest-framework的请求认证流程



一个请求进来首先会进view的dispatch()方法：

```python
    def dispatch(self, request, *args, **kwargs):
        """
        `.dispatch()` is pretty much the same as Django's regular dispatch,
        but with extra hooks for startup, finalize, and exception handling.
        """
        self.args = args
        self.kwargs = kwargs
        # 初始化一个request，
        request = self.initialize_request(request, *args, **kwargs)
        self.request = request
        self.headers = self.default_response_headers  # deprecate?

        try:
            # 这里就是做一些初始化验证：登录验证，权限验证
            self.initial(request, *args, **kwargs)

            # Get the appropriate handler method
            if request.method.lower() in self.http_method_names:
                handler = getattr(self, request.method.lower(),
                                  self.http_method_not_allowed)
            else:
                handler = self.http_method_not_allowed

            response = handler(request, *args, **kwargs)

        except Exception as exc:
            response = self.handle_exception(exc)

        self.response = self.finalize_response(request, response, *args, **kwargs)
        return self.response
```

我们看看initialize_request

```python
    def initialize_request(self, request, *args, **kwargs):
        """
        Returns the initial request object.
        """
        parser_context = self.get_parser_context(request)

        return Request(
            request,
            parsers=self.get_parsers(),
            authenticators=self.get_authenticators(),
            negotiator=self.get_content_negotiator(),
            parser_context=parser_context
        )
```

这里看出，返回了一个新的Request对象，并对一些属性进行赋值，这里注意`authenticators=self.get_authenticators()`这个赋值，这个就是把viewset里面初始化时候设置的`authentication_classes`验证对象进行赋值到Request对象的authenticators属性上，这里就是做用户验证的，待会会说到这个，这里先注意一下。

接下来我们便走到了验证这一步，我们看看initial方法：

```python
    def initial(self, request, *args, **kwargs):
        """
        Runs anything that needs to occur prior to calling the method handler.
        """
        self.format_kwarg = self.get_format_suffix(**kwargs)

        # Perform content negotiation and store the accepted info on the request
        neg = self.perform_content_negotiation(request)
        request.accepted_renderer, request.accepted_media_type = neg

        # Determine the API version, if versioning is in use.
        version, scheme = self.determine_version(request, *args, **kwargs)
        request.version, request.versioning_scheme = version, scheme

        # Ensure that the incoming request is permitted
        self.perform_authentication(request)  # 这里就进行了用户验证
        self.check_permissions(request)
        self.check_throttles(request)
```

我们进perform_authentication()方法看看：

```python
    def perform_authentication(self, request):
        request.user
```

嗯？就这么一句？什么鬼？这特么去哪里验证了？莫慌，我们去看看Request对象里面有啥：

```python
class Request(object):
	# ... 省略n多代码
	@property
    def user(self):
        """
        Returns the user associated with the current request, as authenticated
        by the authentication classes provided to the request.
        """
        if not hasattr(self, '_user'):
            self._authenticate()
        return self._user
```

我们发现request的user是一个属性方法，这里调用了self._authenticate()，跟进去看看：

```python
    def _authenticate(self):
        """
        Attempt to authenticate the request using each authentication instance
        in turn.
        Returns a three-tuple of (authenticator, user, authtoken).
        """
        for authenticator in self.authenticators:
            try:
                user_auth_tuple = authenticator.authenticate(self)
            except exceptions.APIException:
                self._not_authenticated()
                raise

            if user_auth_tuple is not None:
                self._authenticator = authenticator
                self.user, self.auth = user_auth_tuple
                return

        self._not_authenticated()
```

这里就遍历了我们之前在initialize_request()方法中赋值的authenticators属性，他里面是存的一个验证对象数组，然后调用authenticate()方法进行用户验证。这里分三种情况                 

> - 1.如果authenticate方法抛出异常，self._not_authenticated()执行  
> -  2.有返回值，必须是元组：（request.user,request.auth） 
> - 3.如果验证返回None，表示当前认证函数不处理，等下一个认证来处理

当都没有返回值，就执行self._not_authenticated(),相当于匿名用户，没有通过认证：

```python
    def _not_authenticated(self):
        self._authenticator = None

        if api_settings.UNAUTHENTICATED_USER:
            self.user = api_settings.UNAUTHENTICATED_USER()
        else:
            self.user = None

        if api_settings.UNAUTHENTICATED_TOKEN:
            self.auth = api_settings.UNAUTHENTICATED_TOKEN()
        else:
            self.auth = None
```

好了，认证流程就基本上讲完了，我们来梳理一下基本流程：

request-->>view.diapatch()-->>重新封装request后返回-->>验证用户(根据view对象的authentication_classes属性来验证)

既然我们熟悉了流程那么我们就可以自己来定义验证类了

```python
from rest_framework import authentication

class Authentication(authentication.BaseAuthentication):

    def authenticate(self, request):
        print 'authenticate'
        token = request._request.GET.get('token')
        if token:
            return (None, None)
        else:
            raise exceptions.AuthenticationFailed('用户认证失败')

    def authenticate_header(self, request):
        pass
```

我们继承一个基类BaseAuthentication，当然你也可以不继承，只要有authenticate()、authenticate_header()这两个方法即可，但是为了规范代码，我们还是继承下基类。

然后我们在我们的视图函数viewset中添加上这个认证

```python
class MyViewset(mixins.RetrieveModelMixin,
                   mixins.UpdateModelMixin,
                   mixins.ListModelMixin,
                   viewsets.GenericViewSet):

    serializer_class = MySerializers
    # 这里进行认证对象的设置，
    authentication_classes = (Authentication,)
```

这样我们访问这个view视图函数时候如果url后面没带token，那就会返回一个“用户认证失败”的一个信息。

另外，如果我们所有接口都需要加认证，总不能我所有接口都去加这个代码吧？

这时候我们就需要在全局加一个认证对象了，我们在APIView类中发现：

```python
class APIView(View):

    # The following policies may be set at either globally, or per-view.
    renderer_classes = api_settings.DEFAULT_RENDERER_CLASSES
    parser_classes = api_settings.DEFAULT_PARSER_CLASSES
    # 这里 这里 配置了默认的认证类对象，
    authentication_classes = api_settings.DEFAULT_AUTHENTICATION_CLASSES
    throttle_classes = api_settings.DEFAULT_THROTTLE_CLASSES
    permission_classes = api_settings.DEFAULT_PERMISSION_CLASSES
    content_negotiation_class = api_settings.DEFAULT_CONTENT_NEGOTIATION_CLASS
    metadata_class = api_settings.DEFAULT_METADATA_CLASS
    versioning_class = api_settings.DEFAULT_VERSIONING_CLASS
```

然后我们找一下这个东西怎么设置，可以发现api_settings是一个APISettings()对象，我们进去看看：

```python
class APISettings(object):
	@property
    def user_settings(self):
        if not hasattr(self, '_user_settings'):
            self._user_settings = getattr(settings, 'REST_FRAMEWORK', {})
        return self._user_settings
```

可以看到他有一个user_settings的属性，通过`REST_FRAMEWORK`这个key来获取，所以我们可以通过在全局设置中添加如下内容：

```python
# 设置全局的认证类
REST_FRAMEWORK = {
    "DEFAULT_AUTHENTICATION_CLASSES":['mysite.auth.Authentication',]
}
```

如上设置后便所有的接口都默认会有这个认证了，如果某个接口不需要全局认证，如下设置即可：

```python
class MyViewset(viewsets.GenericViewSet):
    authentication_classes = []
```



## 总结

**系统认证流程：**

> 1. request
>
> 2. view.diapatch()
>
> 3. 重新封装request后返回
>
> 4. 验证用户(根据view对象的authentication_classes属性来验证) 
>
> 5. 对request中的user进行设置（方便全局使用）

**自建认证类流程：**

> 1. 继承BaseAuthentication 
>
> 2. 重写authenticate方法
>
> 3. authenticate_header方法直接写pass就可以（这个方法必须写）
>
> 4. 在viewset中加入自定义的认证类

**认证方法authenticate()返回值（三种）**

> - None ----->>>当前认证不管，等下一个认证来执行
> -  raise exceptions.AuthenticationFailed('用户认证失败')       # from rest_framework import exceptions
> -  有返回值元祖形式：（元素1，元素2）      #元素1复制给request.user;  元素2复制给request.auth

**使用**

- 局部：authentication_classes = [BaseAuthentication,]

- 全局使用

  ```python
  #设置全局认证
  REST_FRAMEWORK = {
      "DEFAULT_AUTHENTICATION_CLASSES":['mysite.auth.Authentication',]
  }
  ```





