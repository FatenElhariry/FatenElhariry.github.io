---
layout: post
title: Model Conventions and Action Constraints
author: Faten Elhariry
categories: dotnetcore
tags: dotnetcore
---

this book review section for Model Conventions and Action Constraints

first .net core used  convention over configuration where you controller and actions can be discovered at runtime by convention 

#### application model
  the discovery process is the application model, which is made up of objects that describe every controller class, action method, and parameter that has been found. 
  ##### Putting the Application Model and Model Conventions in Context
    - What are they?
      The application model is a complete description of the controllers and actions that have been discovered in the application. Model conventions allow custom changes to be applied to the application model.
    - Why are they useful?
      Model conventions are useful because they allow changes to the way that classes and methods are mapped to controllers and actions. Other customizations can be performed, such as restricting the HTTP methods that an action accepts or applying action constraints (which are described later in this chapter).
    - How are they used?
      Model conventions are defined using a range of interfaces, described in the following sections, and applied as attributes or configured in the Startup class.
  ##### Understanding the Application Model
    During the discovery process, MVC creates an instance of the ApplicationModel class and populates it with details of the controllers and actions that it finds. When the discovery process is complete, model conventions are applied to make any custom changes you specify
    in `Microsoft.AspNetCore.Mvc.ApplicationModels.ApplicationModel` 
    - **ApplicationModel Properties**
      - **Controllers**: 
        This property returns an `IList<ControllerModel>` that contains all of the controllers in the application. 
        -  **ControllerModel Properties**
           -  
      - **Filters**
        This property returns an `IList<IFilterMetadata>` that contains the global filters in the application.
  #### Four kinds of model conventions
    - **IApplicationModelConvention**
      - This interface is used to apply a convention to the ApplicationModel object.
    -  **IControllerModelConvention**
      This interface is used to apply a convention to the ControllerModel objects in the application model.
    - **IActionModelConvention**
      This interface is used to apply a convention to the ActionModel objects in the application model.
    - **IParameterModelConvention**
      This interface is used to apply a convention to the ParameterModel objects in the application model.


    _controller model conventions are applied first, followed by action model conventions, and, finally, parameter model conventions._