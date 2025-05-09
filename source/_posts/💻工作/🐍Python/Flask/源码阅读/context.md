---
title: Flask 源码解析：上下文
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
date: 2019-08-23 12:27:56
---
这是 Flask 源码解析系列文章的其中一篇，本系列所有文章列表：

* [Flask 源码解析：简介](https://cizixs.com/2017/01/10/flask-insight-introduction)
* [Flask 源码解析：应用启动流程](https://cizixs.com/2017/01/11/flask-insight-start-process)
* [Flask 源码解析：路由](https://cizixs.com/2017/01/12/flask-insight-routing)
* [Flask 源码解析：上下文](https://cizixs.com/2017/01/13/flask-insight-context)
* [Flask 源码解析：请求](https://cizixs.com/2017/01/18/flask-insight-request)
* [Flask 源码解析：响应](https://cizixs.com/2017/01/22/flask-insight-response)
* [Flask 源码解析：session](https://cizixs.com/2017/03/08/flask-insight-session)

## 构建路由规则

一个 web 应用不同的路径会有不同的处理函数，**路由就是根据请求的 URL 找到对应处理函数的过程。**

在执行查找之前，需要有一个规则列表，它存储了 url 和处理函数的对应关系。最容易想到的解决方案就是定义一个字典，key 是 url，value 是对应的处理函数。如果 url 都是静态的（url 路径都是实现确定的，没有变量和正则匹配），那么路由的过程就是从字典中通过 url 这个 key ，找到并返回对应的 value；如果没有找到，就报 404 错误。而对于动态路由，还需要更复杂的匹配逻辑。flask 中的路由过程是这样的吗？这篇文章就来分析分析。

在分析路由匹配过程之前，我们先来看看 `flask` 中，[构建这个路由规则](http://flask.pocoo.org/docs/0.12/api/ target=)的两种方法：

1.  通过 [\`@app.route](https://cizixs.com/2017/01/12/flask-insight-routing/mailto:`@app.route)()\` decorator，比如文章开头给出的 hello world 例子
2.  通过 `app.add_url_rule`，这个方法的签名为 `add_url_rule(self, rule, endpoint=None, view_func=None, **options)`，参数的含义如下：
    *   `rule`： url 规则字符串，可以是静态的 `/path`，也可以包含 `/`
    *   `endpoint`：要注册规则的 endpoint，默认是 `view_func` 的名字
    *   `view_func`：对应 url 的处理函数，也被称为视图函数

这两种方法是等价的，也就是说：
```python
    @app.route('/')
    def hello():
        return "hello, world!"

```
也可以写成
```python
    def hello():
        return "hello, world!"

    app.add_url_rule('/', 'hello', hello)
```

**NOTE**: 其实，还有一种方法来构建路由规则——直接操作 `app.url_map` 这个数据结构。不过这种方法并不是很常用，因此就不展开了。

注册路由规则的时候，flask 内部做了哪些东西呢？我们来看看 `route` 方法：
```python
    def route(self, rule, **options):
        """A decorator that is used to register a view function for a
        given URL rule.  This does the same thing as :meth:`add_url_rule`
        but is intended for decorator usage.
        """

        def decorator(f):
            endpoint = options.pop('endpoint', None)
            self.add_url_rule(rule, endpoint, f, **options)
            return f

        return decorator
```

`route` 方法内部也是调用 `add_url_rule`，只不过在外面包了一层装饰器的逻辑，这也验证了上面两种方法等价的说法。
```python
    def add_url_rule(self, rule, endpoint=None, view_func=None, **options):
        """Connects a URL rule.  Works exactly like the :meth:`route`
        decorator.  If a view_func is provided it will be registered with the
        endpoint.
        """

        methods = options.pop('methods', None)

        rule = self.url_rule_class(rule, methods=methods, **options)
        self.url_map.add(rule)
        if view_func is not None:
            old_func = self.view_functions.get(endpoint)
            if old_func is not None and old_func != view_func:
                raise AssertionError('View function mapping is overwriting an '
                                     'existing endpoint function: %s' % endpoint)
            self.view_functions[endpoint] = view_func

```
上面这段代码省略了处理 endpoint 和构建 methods 的部分逻辑，可以看到它主要做的事情就是更新 `self.url_map` 和 `self.view_functions` 两个变量。找到变量的定义，发现 `url_map` 是 `werkzeug.routeing:Map` 类的对象，`rule` 是 `werkzeug.routing:Rule` 类的对象，`view_functions` 就是一个字典。这和我们之前预想的并不一样，这里增加了 `Rule` 和 `Map` 的封装，还把 `url` 和 `view_func` 保存到了不同的地方。

需要注意的是：每个视图函数的 endpoint 必须是不同的，否则会报 `AssertionError`。

## werkzeug 路由逻辑

事实上，flask 核心的路由逻辑是在 `werkzeug` 中实现的。所以在继续分析之前，我们先看一下 `werkzeug` 提供的[路由功能](http://werkzeug.pocoo.org/docs/0.11/routing/)。
```python
    >>> m = Map([
    ...     Rule('/', endpoint='index'),
    ...     Rule('/downloads/', endpoint='downloads/index'),
    ...     Rule('/downloads/<int:id>', endpoint='downloads/show')
    ... ])
    >>> urls = m.bind("example.com", "/")
    >>> urls.match("/", "GET")
    ('index', {})
    >>> urls.match("/downloads/42")
    ('downloads/show', {'id': 42})

    >>> urls.match("/downloads")
    Traceback (most recent call last):
      ...
    RequestRedirect: http://example.com/downloads/
    >>> urls.match("/missing")
    Traceback (most recent call last):
      ...
    NotFound: 404 Not Found
```

上面的代码演示了 `werkzeug` 最核心的路由功能：添加路由规则（也可以使用 `m.add`），把路由表绑定到特定的环境（`m.bind`），匹配 url（`urls.match`）。正常情况下返回对应的 endpoint 名字和参数字典，可能报重定向或者 404 异常。

可以发现，[`endpoint` 在路由过程中非常重要](https://stackoverflow.com/a/19262349/1925083)。`werkzeug` 的路由过程，其实是 url 到 endpoint 的转换：通过 url 找到处理该 url 的 endpoint。至于 endpoint 和 view function 之间的匹配关系，`werkzeug` 是不管的，而上面也看到 `flask` 是把这个存放到字典中的。

## flask 路由实现

好，有了这些基础知识，我们回头看 `dispatch_request`，继续探寻路由匹配的逻辑：
```python
    def dispatch_request(self):
        """Does the request dispatching.  Matches the URL and returns the
        return value of the view or error handler.  This does not have to
        be a response object.  In order to convert the return value to a
        proper response object, call :func:`make_response`.
        """

        req = _request_ctx_stack.top.request
        if req.routing_exception is not None:
            self.raise_routing_exception(req)
        rule = req.url_rule

        # dispatch to the handler for that endpoint
        return self.view_functions[rule.endpoint](**req.view_args)

```
这个方法做的事情就是找到请求对象 `request`，获取它的 `endpoint`，然后从 `view_functions` 找到对应 `endpoint` 的 `view_func` ，把请求参数传递过去，进行处理并返回。`view_functions` 中的内容，我们已经看到，是在构建路由规则的时候保存进去的；那请求中 `req.url_rule` 是什么保存进去的呢？它的格式又是什么？

我们可以先这样理解：`_request_ctx_stack.top.request` 保存着当前请求的信息，在每次请求过来的时候，`flask` 会把当前请求的信息保存进去，这样我们就能在整个请求处理过程中使用它。至于怎么做到并发情况下信息不会相互干扰错乱，我们将在下一篇文章介绍。

`_request_ctx_stack` 中保存的是 `RequestContext` 对象，它出现在 `flask/ctx.py` 文件中，和路由相关的逻辑如下：
```python
    class RequestContext(object):
        def __init__(self, app, environ, request=None):
            self.app = app
            self.request = request
            self.url_adapter = app.create_url_adapter(self.request)
            self.match_request()

        def match_request(self):
            """Can be overridden by a subclass to hook into the matching
            of the request.
            """
            try:
                url_rule, self.request.view_args = \
                    self.url_adapter.match(return_rule=True)
                self.request.url_rule = url_rule
            except HTTPException as e:
                self.request.routing_exception = e


    class Flask(_PackageBoundObject):
        def create_url_adapter(self, request):
            """Creates a URL adapter for the given request.  The URL adapter
            is created at a point where the request context is not yet set up
            so the request is passed explicitly.
            """
            if request is not None:
                return self.url_map.bind_to_environ(request.environ,
                    server_name=self.config['SERVER_NAME'])
```

在初始化的时候，会调用 `app.create_url_adapter` 方法，把 `app` 的 `url_map` 绑定到 WSGI environ 变量上（`bind_to_environ` 和之前的 `bind` 方法作用相同）。最后会调用 `match_request` 方法，这个方式调用了 `url_adapter.match` 方法，进行实际的匹配工作，返回匹配的 url rule。而我们之前使用的 `url_rule.endpoint` 就是匹配的 endpoint 值。

整个 `flask` 的路由过程就结束了，总结一下大致的流程：

*   通过 [\`@app.route](https://cizixs.com/2017/01/12/flask-insight-routing/mailto:`@app.route)`或者`app.add\_url\_rule\` 注册应用 url 对应的处理函数
*   每次请求过来的时候，会事先调用路由匹配的逻辑，把路由结果保存起来
*   `dispatch_request` 根据保存的路由结果，调用对应的视图函数

## match 实现

虽然讲完了 `flask` 的路由流程，但是还没有讲到最核心的问题：`werkzeug` 中是怎么实现 `match` 方法的。`Map` 保存了 `Rule` 列表，`match` 的时候会依次调用其中的 `rule.match` 方法，如果匹配就找到了 match。`Rule.match` 方法的代码如下：
```python
    def match(self, path):
            """Check if the rule matches a given path. Path is a string in the
            form ``"subdomain|/path(method)"`` and is assembled by the map.  If
            the map is doing host matching the subdomain part will be the host
            instead.

            If the rule matches a dict with the converted values is returned,
            otherwise the return value is `None`.
            """
            if not self.build_only:
                m = self._regex.search(path)
                if m is not None:
                    groups = m.groupdict()

                    result = {}
                    for name, value in iteritems(groups):
                        try:
                            value = self._converters[name].to_python(value)
                        except ValidationError:
                            return
                        result[str(name)] = value
                    if self.defaults:
                        result.update(self.defaults)

                    return result
```

它的逻辑是这样的：用实现 compile 的正则表达式去匹配给出的真实路径信息，把所有的匹配组件转换成对应的值，保存在字典中（这就是传递给视图函数的参数列表）并返回。
