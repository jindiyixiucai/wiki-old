---
title: Flask 源码解析：响应
toc: true
tags:
  - Flask
  - web 开发
  - Python
categories:
  - "\U0001F4BB 工作"
  - "\U0001F40DPython"
  - Flask
  - 源码阅读
date: 2019-08-25 12:27:56
---
这是 Flask 源码解析系列文章的其中一篇，本系列所有文章列表：

* [Flask 源码解析：简介](https://cizixs.com/2017/01/10/flask-insight-introduction)
* [Flask 源码解析：应用启动流程](https://cizixs.com/2017/01/11/flask-insight-start-process)
* [Flask 源码解析：路由](https://cizixs.com/2017/01/12/flask-insight-routing)
* [Flask 源码解析：上下文](https://cizixs.com/2017/01/13/flask-insight-context)
* [Flask 源码解析：请求](https://cizixs.com/2017/01/18/flask-insight-request)
* [Flask 源码解析：响应](https://cizixs.com/2017/01/22/flask-insight-response)
* [Flask 源码解析：session](https://cizixs.com/2017/03/08/flask-insight-session)

## response 简介

在 flask 应用中，我们只需要编写 view 函数，并不需要直接和响应（response）打交道，flask 会自动生成响应返回给客户端。

> The return value from a view function is automatically converted into a response object for you.
> —— Flask docs

我们知道 HTTP 响应分为三个部分：
状态栏（HTTP 版本、状态码和说明）、头部（以冒号隔开的字符对，用于各种控制和协商）、body（服务端返回的数据）。比如下面访问[博客首页](https://cizixs.com)的响应：
```plain
    HTTP/1.1 200 OK

    Access-Control-Allow-Origin: *
    Cache-Control: max-age=600
    Content-Encoding: gzip
    Content-Type: text/html; charset=utf-8
    Date: Wed, 15 Feb 2017 07:50:41 GMT
    Expires: Wed, 15 Feb 2017 08:00:41 GMT
    Last-Modified: Wed, 15 Feb 2017 07:46:56 GMT
    Server: GitHub.com
    Transfer-Encoding: chunked
    X-GitHub-Request-Id: D2A7:7B6B:33C0628:47C44B9:58A40851

    <BODY>

```
flask 自然也会提供所有这些数据的操作，视图函数就支持返回三个值：第一个是返回的数据，第二个是状态码，第三个是头部字典。比如：
```python
    @app.route('/')
    def hello_world():
        return 'Hello, World!', 201, {'X-Foo': 'bar'}
```

这篇文章就讲讲这背后的魔法。

## flask 响应（response）

在 [Flask 源码解析：应用启动流程](https://cizixs.com/2017/01/11/flask-insight-start-process) 的最后，我们讲到 `full_dispatch_request` 在调用路由的视图函数之后，会调用 `finalize_request` 进行最后的处理，在这个方法里就包含了 response 对象的生成和处理逻辑。

`finalize_request` 的代码如下：
```python
    def finalize_request(self, rv, from_error_handler=False):
        """Given the return value from a view function this finalizes
        the request by converting it into a response and invoking the
        postprocessing functions.  This is invoked for both normal
        request dispatching as well as error handlers.
        """
        response = self.make_response(rv)
        try:
            response = self.process_response(response)
            request_finished.send(self, response=response)
        except Exception:
            if not from_error_handler:
                raise
            self.logger.exception('Request finalizing failed with an '
                                  'error while handling an error')
        return response
```

里面有两个方法调用：`make_response` 根据视图函数的返回值生成 response 对象，`process_response` 对 response 做一些后续的处理（比如执行 hooks 函数）。我们先来看看 `make_response`：
```python
    def make_response(self, rv):
        """Converts the return value from a view function to a real
        response object that is an instance of :attr:`response_class`.
        """
        status_or_headers = headers = None
        if isinstance(rv, tuple):
            rv, status_or_headers, headers = rv + (None,) * (3 - len(rv))

        if isinstance(status_or_headers, (dict, list)):
            headers, status_or_headers = status_or_headers, None

        if not isinstance(rv, self.response_class):
            # When we create a response object directly, we let the constructor
            # set the headers and status.  We do this because there can be
            # some extra logic involved when creating these objects with
            # specific values (like default content type selection).
            if isinstance(rv, (text_type, bytes, bytearray)):
                rv = self.response_class(rv, headers=headers,
                                         status=status_or_headers)
                headers = status_or_headers = None

        if status_or_headers is not None:
            if isinstance(status_or_headers, string_types):
                rv.status = status_or_headers
            else:
                rv.status_code = status_or_headers
        if headers:
            rv.headers.extend(headers)

        return rv

```
`make_response` 是视图函数能返回多个不同数量和类型值的关键，因为它能处理这些情况，统一把它们转换成 response。
如果返回值本身就是 Response 实例，就直接使用它；如果返回值是字符串类型，就把它作为响应的 body，并自动设置状态码和头部信息；
如果返回值是 tuple，会尝试用 (response, status, headers) 或者 (response, headers) 去解析。

**NOTE**：因为视图函数可以返回 `Response` 对象，因此我们可以直接操作 `Response`。

不管视图函数返回的是什么，最终都会变成 `Response` 对象，那么我们就来看看 `Response` 的定义：
```python
    from werkzeug.wrappers import Response as ResponseBase


    class Response(ResponseBase):
        """The response object that is used by default in Flask.  Works like the
        response object from Werkzeug but is set to have an HTML mimetype by
        default.  Quite often you don't have to create this object yourself because
        :meth:`~flask.Flask.make_response` will take care of that for you.

        If you want to replace the response object used you can subclass this and
        set :attr:`~flask.Flask.response_class` to your subclass.
        """
        default_mimetype = 'text/html'
```

Flask 的 `Response` 类非常简单，它只是继承了 `werkzeug.wrappers:Response`，然后设置默认返回类型为 html。
不过从注释中，我们得到两条很有用的信息：

1.  一般情况下不要直接操作 `Response` 对象，而是使用 `make_response` 方法来生成它
2.  如果需要使用自定义的响应对象，可以覆盖 flask app 对象的 `response_class` 属性。

继续，下面就要分析 werkzeug 对应的代码了。

## werkzeug response

werkzeug 实现的 response 定义在 `werkzeug/wrappers.py` 文件中：
```python
    class Response(BaseResponse, ETagResponseMixin, ResponseStreamMixin,
                   CommonResponseDescriptorsMixin,
                   WWWAuthenticateMixin):

        """Full featured response object implementing the following mixins:

        - :class:`ETagResponseMixin` for etag and cache control handling
        - :class:`ResponseStreamMixin` to add support for the `stream` property
        - :class:`CommonResponseDescriptorsMixin` for various HTTP descriptors
        - :class:`WWWAuthenticateMixin` for HTTP authentication support
        """
```

和我们在 [flask 请求](https://cizixs.com/2017/01/18/flask-insight-request)分析的 Request 类一样，这里使用了 Mixin 机制。`BaseResponse` 精简后的大概框架如下：
```python
    class BaseResponse(object):
        """Base response class.  The most important fact about a response object
        is that it's a regular WSGI application.  It's initialized with a couple
        of response parameters (headers, body, status code etc.) and will start a
        valid WSGI response when called with the environ and start response
        callable.
        """

        charset = 'utf-8'
        default_status = 200
        default_mimetype = 'text/plain'
        automatically_set_content_length = True

        def __init__(self, response=None, status=None, headers=None,
                     mimetype=None, content_type=None, direct_passthrough=False):
            pass

```
`BaseResponse` 有一些类属性，定义了默认的值，比如默认字符编码是 utf-8，默认状态码是 200 等。实例化的时候接受的参数有：

*   response： 字符串或者其他 iterable 对象，作为响应的 body
*   status： 状态码，可以是整数，也可以是字符串
*   headers： 响应的头部，可以是个列表，也可以是 `werkzeug.datastructures.Headers` 对象
*   mimetype： mimetype 类型，告诉客户端响应 body 的格式，默认是文本格式
*   content\_type: 响应头部的 `Content-Type` 内容

所有这些参数都是可选的，默认情况下会生成一个状态码为 200，没有任何 body 的响应。status、status\_code 作为 `Response` 的属性，可以直接读取和修改。body 数据在内部保存为 iterable 的类型，
但是对外也提供了直接读写的接口 `self.data`：
```python
        def get_data(self, as_text=False):
            """The string representation of the request body.  Whenever you call
            this property the request iterable is encoded and flattened.
            """
            self._ensure_sequence()
            rv = b''.join(self.iter_encoded())
            if as_text:
                rv = rv.decode(self.charset)
            return rv

        def set_data(self, value):
            """Sets a new string as response.  The value set must either by a
            unicode or bytestring.
            """
            if isinstance(value, text_type):
                value = value.encode(self.charset)
            else:
                value = bytes(value)
            self.response = [value]
            if self.automatically_set_content_length:
                self.headers['Content-Length'] = str(len(value))

        data = property(get_data, set_data, doc='''
            A descriptor that calls :meth:`get_data` and :meth:`set_data`.  This
            should not be used and will eventually get deprecated.
            ''')
```

body 字符的编码和长度都是自动设置的，用户不需要手动处理。

至于头部的存储，werkzeug 使用的是类似于字典的 `werkzeug.datastructures:Headers` 类。在[Flask 源码解析：请求](https://cizixs.com/2017/01/18/flask-insight-request)这篇文章中，我们没有详细
解释头部的存储，那么这篇文章就具体分析一下吧。

`Headers` 这个类的提供了很多和字典相同的接口：keys、values、iterms，但是和字典的区别在于它保存的值是有序的，而且允许相同 key 的值存在。
为什么这么设计呢？因为着更符合 HTTP 头部的特性。先来看看有序，在 HTTP 传送的过程中，如果头部各个 key-value 键值对顺序发生变化，有些代理或者客户端等组件会认为请求被篡改而丢弃或者拒绝请求的处理，所以最好把头部设置为有序的，用户按照什么顺序设置的，就按照什么顺序存储；再说说相同 key 的问题，这是因为 HTTP 头部同一个 key 可能有多个 value（比如 Accept、SetCookie 头部）。那么这个看起比较特殊的字典是怎么实现的呢？来看代码：
```python
    class Headers(object):
        """An object that stores some headers.  It has a dict-like interface
        but is ordered and can store the same keys multiple times.
        """

        def __init__(self, defaults=None):
            self._list = []
            if defaults is not None:
                if isinstance(defaults, (list, Headers)):
                    self._list.extend(defaults)
                else:
                    self.extend(defaults)

        def __getitem__(self, key, _get_mode=False):
            if not _get_mode:
                if isinstance(key, integer_types):
                    return self._list[key]
                elif isinstance(key, slice):
                    return self.__class__(self._list[key])
            if not isinstance(key, string_types):
                raise exceptions.BadRequestKeyError(key)
            ikey = key.lower()
            for k, v in self._list:
                if k.lower() == ikey:
                    return v
            if _get_mode:
                raise KeyError()
            raise exceptions.BadRequestKeyError(key)
```

可以看到，头部信息在内部存储为二元组构成的列表，这样就能同时保证它的有序性和重复性。一个核心的方法是 `__getitem__`，它定义了如何获取头部中的信息：

*   通过下标 `header[3]`，直接返回对应未知存储的键值对元组
*   通过 key，返回 value `header['Accept']`，返回匹配的第一个 value 值
*   通过 slice `header[3:7]`，返回另外一个 `Headers` 对象，保存了 slice 中所有的数据

然后实现 `keys()`、`items()`、`pop()`、`setdefault()` 等方法让它表现出来字典的特性，除此之外还有 `add()`、`extend()`、`add_header()` 等和字典无关的方法方便操作。

## 自定义 response

如果需要扩展 flask `Response` 的功能，或者干脆把它替换掉，只要修改 flask app 的 `response_class` 属性就可以了，比如：
```python
    from flask import Flask, Response

    class MyResponse(Response):
        pass

    app = Flask(__name__)
    app.response_class = MyResponse

```
更多可能的用法，可以参考文章末尾的参考资料。

## 参考资料

*   [customizing the flask response class](https://blog.miguelgrinberg.com/post/customizing-the-flask-response-class)