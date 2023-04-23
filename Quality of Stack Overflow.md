https://stackoverflow.com/questions/4045161/should-i-use-a-class-or-dictionary

# The Question

I have a class that contains only fields and no methods, like this:

```
class Request(object):

    def __init__(self, environ):
        self.environ = environ
        self.request_method = environ.get('REQUEST_METHOD', None)
        self.url_scheme = environ.get('wsgi.url_scheme', None)
        self.request_uri = wsgiref.util.request_uri(environ)
        self.path = environ.get('PATH_INFO', None)
        # ...
```

This could easily be translated to a dict. The class is more flexible for future additions and could be fast with `__slots__`. So would there be a benefit of using a dict instead? Would a dict be faster than a class? And faster than a class with slots?

# The accepted Answer

Why would you make this a dictionary? What's the advantage? What happens if you later want to add some code? Where would your `__init__` code go?

Classes are for bundling related data (and usually code).

Dictionaries are for storing key-value relationships, where usually the keys are all of the same type, and all the values are also of one type. Occasionally they can be useful for bundling data when the key/attribute names are not all known up front, but often this a sign that something's wrong with your design.

Keep this a class.
