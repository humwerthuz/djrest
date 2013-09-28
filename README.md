DjRest - Simple and beatiful resting for Django
================================

DjRest is a simple api for creating restfull webservices with django using a flask like syntax.

Usage
-----------------------
First things first, you need an existing Django project.

Now you can create a resource app, see example below

    django_project/
                  django_project/
                                some_files
                                urls.py
                  some_app/
                  another_app/
                  resource/
                          __init__.py
                          api/
                              __init__.py
                              operations.py
                              resource.py
    
In this example you see resource dir wich contains api module wich contains operations and resource modules.

Now let's de declare a resource class (resource.py file):


    from djrest.common.api import Resource
    from resource.api import operations
    
    
    class TestResource(Resource):
        def __init__(self):
            super(TestResource, self).__init__()
            self.resource_name = "test"
            self.register_rest_operation("v1", operations.TestOperation)
            
Now let's declare TestOperation class (operations.py file):

    from djrest.common.api import Rest
    from djrest.http.responses import JsonResponse
    
    @Rest.route("/", methods=["GET"])
    class TestOperation(Rest):
        return JsonResponse({
            'status': 'all success'
        })
        
Now and finally you must add this in urls.py:

    api = RestApi(api_name="my api name") #defaults 'api'
    api.register_resource(TestResource())
    
    urlpatterns = patterns('',
        ...,
        ...,
        url(r'%s' % api.base_url, include(api.urls))
    )
    
Supported Methods
-------------------------

    GET
    POST
    DELETE
    PUT
    PATCH
    
Each of this methods will call the corresponding resource function. For example if POST is declared in methods list, when an http request with POST is issued on the resource the api will call the "post" function of the resource.

Other Goodness
-------------------------

You can specify parameters for your urls like this:

    @Rest.route("/{id:int}", methods=["GET", "POST"])
    class TestResource(Resource):
        pass
      
    @Rest.route("/{id:int}/update_name/{name:str}", methods=["GET", "POST"])
    class TestResource(Resource):
        pass

In case you want to access the same resource without the parameters you can do as following

    @Rest.route("/{id:int}", allow_empty_args=True, methods=["GET"])
    class TestResource(Resource):
        def get(self, request, id=None):
            pass

But be warned that any parameter you may be using in your http functions MUST use a default value
otherwise your request will fail because your function wall have missing values.

A little bit like Flask :)
