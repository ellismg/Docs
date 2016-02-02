Actions and Action Results
==========================

By `Steve Smith`_

Actions and action results are a fundamental part of how developers build apps using ASP.NET MVC.

Sections:
	- `What is a Controller`_
	- `Defining Actions`_

`View or download sample from GitHub <https://github.com/aspnet/Docs/tree/1.0.0-rc1/mvc/performance/response-caching/sample>`_.

What is a Controller
--------------------

In ASP.NET MVC, a `Controller` is used to define and group a set of actions. An `action` (or `action method`) is a method on a controller that handles incoming requests. In ASP.NET 5, a controller can be any instantiable class that ends in "Controller" or inherits from a class that ends with "Controller". By convention, controller classes may be placed within a root-level "Controllers" folder within the app, and may inherit from ``Microsoft.AspNet.Mvc.Controller``, but these are not required.

Controllers support :doc:`dependency injection <dependency-injection>`, and should follow the `Explicit Dependencies Principle <http://deviq.com/explicit-dependencies-principle>`_ and request any dependencies their actions require through their constructor. Controllers provide a logical means of grouping similar actions together, allowing common sets of rules (e.g. routing, caching, authorization) to be applied collectively.

Within the Model-View-Controller pattern, a Controller is responsible for the initial processing of the request and instantiation of the Model. Business decisions should ideally be performed within the Model. The Controller then takes the result of the Model's processing (if any), chooses which View should be returned, and returns that View along with any data that View requires. Learn more: :doc:`../overview`.

.. tip:: The Controller is a `UI level` abstraction. Its responsibility is to ensure incoming request data is valid and to choose which View (or result) should be returned. In well-factored apps it will not include data access or business logic, but instead will delegate to services handling these responsibilities.
 
Defining Actions
----------------
Any public method on a controller type is an action. Parameters on actions are bound to request data and validated using :doc:`model binding <../models/model-binding>`. Any action that accepts parameters should verify that the incoming data is valid by checking ``ModelState.IsValid``.

.. warning:: Action methods that accept parameters should always check ``ModelState.IsValid`` as part of their processing.

Action methods should contain logic for mapping an incoming request to a business concern. These business concerns should typically be represented as services that your controller accesses through dependency injection. Actions then map the result of the business action to an application state.

Actions can return an ``IActionResult`` that produces a response. You can either create and return your own ``ActionResult`` directly, or you can use one of the many helper methods available on the Controller base class.

Controller Helper Methods
#########################

Inheriting from the base ``Controller`` class provides the following helper methods for returning various kinds of responses.

View
	Returns a View that uses a model to render HTML. Example: ``return View(customer);``

HTTP Status Code
	Return an HTTP status code. Examples: ``return Ok();`` or ``return BadRequest();``

Formatted Response
	Return ``Json`` or similar to format an object in a specific manner. Example: ``return Json(customer);``

An object
	Return an object you want formatted based on the client's request. Learn more about :doc:`../models/formatting`

Cross-Cutting Concerns
######################

In most apps, many actions will share parts of their workflow. For instance, most of an app might be available only to authenticated users, or might benefit from caching. Try to keep your actions from growing too large by using :doc:`filters` to handle these concerns. This can help eliminate duplication within your actions, allowing them to follow the `Don't Repeat Yourself (DRY) principle <http://deviq.com/don-t-repeat-yourself/>`_.

In the case of authorization and authentication, you can apply the ``Authorize`` attribute to any actions that require it. Adding this attribute will ensure the appropriate ``ActionFilter`` is applied to any request for this action. Attributes can also be applied at the ``Controller`` level, so groups of actions with similar behavior can use controller-level attributes to apply the same filters to every action. Action-level attributes will override controller-level attributes, so for example a controller could be marked with the ``Authorize`` attribute, but one of its actions could override this with the ``AllowAnonymous`` attribute. Learn more about :doc:`../security/authorization-filters`.

Other examples of cross-cutting concerns in MVC apps may include:
	* :doc:`error-handling`
	* :doc:`../performance/response-caching`

.. note:: Many cross-cutting concerns can be handled using filters in MVC apps. Another option to keep in mind that is available to any ASP.NET app is `custom middleware <https://docs.asp.net/en/latest/fundamentals/middleware.html>`_.
