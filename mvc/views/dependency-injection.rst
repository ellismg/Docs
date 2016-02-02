Injecting a Service Into a View
===============================

By `Steve Smith`_, `Rick Anderson`_

ASP.NET MVC 6 supports `dependency injection <https://docs.asp.net/en/latest/fundamentals/dependency-injection.html>`_ into views. This can be useful for view-specific services, such as localization or data required only for populating view elements.

.. TODO: Add localization sample in RC2 timeframe.

`View sample files <https://github.com/aspnet/Docs/tree/1.0.0-rc1/mvc/views/dependency-injection/sample>`_

.. warning:: When applying this functionality, ensure you maintain `separation of concerns <http://deviq.com/separation-of-concerns>`_ between your controllers and views. Most of the data your views  display should be passed in from the controller. This technique is recommended only for view-specific data.

A Simple Example
----------------
With the above warning in mind, you can inject a service into a view using the ``@inject`` directive, as shown below:

.. literalinclude:: dependency-injection/sample/src/ViewInjectSample/Views/Todo/Index.cshtml
  :linenos:
  :language: c#
  :emphasize-lines: 4-5,15-17

This view displays a list of ``ToDoItem`` instances, along with a summary showing overall statistics. The summary is populated from the injected ``StatisticsService``. This service is registered for dependency injection in ``ConfigureServices`` in ``Startup.cs``:

.. literalinclude:: dependency-injection/sample/src/ViewInjectSample/Startup.cs
  :linenos:
  :lines: 15-22
  :language: c#
  :emphasize-lines: 5-6
  :dedent: 8
  
The ``StatisticsService`` simply performs some calculations on the set of ``ToDoItem`` instances, which it accesses via a repository:


.. literalinclude:: dependency-injection/sample/src/ViewInjectSample/Model/Services/StatisticsService.cs
  :linenos:
  :language: c#
  :emphasize-lines: 16,21,27

.. note:: In this sample the repository uses an in-memory collection. The implementation shown above is not recommended for large, remotely accessed data sets.

The end result of this approach is the display of data from both the model bound to the view as well as the service injected into the view:

.. image:: dependency-injection/_static/screenshot.png

Populating Lookup Data
----------------------
One example where you may want to use view injection is population of options for certain UI elements, such as dropdown lists. Consider a user profile form that includes options for specifying gender, state, and other preferences. Rendering such a form using a standard MVC approach would require the controller to request data access services for each of these sets of options, and then populate a model or ``ViewBag`` with each set of options to be bound.

An alternative approach leverages services injected directly into the view for these common sets of options. This minimizes the amount of code required by the controller, moving this view element construction logic into the view itself. The Controller action to display a profile editing form only needs to pass the form the profile instance:

.. literalinclude:: dependency-injection/sample/src/ViewInjectSample/Controllers/ProfileController.cs
  :linenos:
  :language: c#
  :emphasize-lines: 9,19

The HTML form used to update these preferences includes dropdown lists for three of the properties:

.. image:: dependency-injection/_static/updateprofile.png

These lists are populated by a service that has been injected into the view:

.. literalinclude:: dependency-injection/sample/src/ViewInjectSample/Views/Profile/Index.cshtml
  :linenos:
  :language: c#
  :emphasize-lines: 4,16-17,21-22,26-27

The ``ProfileOptionsService`` is a UI-level service designed to provide just the data needed for this form:

.. literalinclude:: dependency-injection/sample/src/ViewInjectSample/Model/Services/ProfileOptionsService.cs
  :linenos:
  :language: c#
  :emphasize-lines: 7,13,24

.. tip:: Don't forget to register types you will request through dependency injection in the  ``ConfigureServices`` method in ``Startup.cs``.

See Also
--------
* Simon Timms Blog: `Getting Lookup Data Into Your View <http://blog.simontimms.com/2015/06/09/getting-lookup-data-into-you-view/>`_