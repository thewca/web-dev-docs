---
title: Passing Data Between React and Rails
parent: Guides
layout: default
---

# Overview

The website is migrating from being a pure Rails project to using React for frontend and Rails for backend. We want to implement this in a decoupled manner - that is to say:
- these systems only communiate via an API
- the frontend should not be aware of how data is stored in the backend
- the backend should not be aware of how data is collected in the frontend
- we should be able to replace frontend/backend with no change necessary to the other component (assuming API endpoints remain the same)

In some cases, it isn't possible to introduce the necessary GET API endpoints yet. In these cases, data from Rails is serialized (converted) into JSON, and passed directly to the `react_component` function in the View file.

# Reading data from Rails

## Process Overview

{: .info}
> Diagrams in this section are available for editing to WST members [here](https://app.diagrams.net/#G1V1mXsYcMw6cIaPJU3cA-s3pJ7psYovLD#%7B%22pageId%22%3A%22wrVvO56TNKLfE-PIFfzB%22%7D).


To understand how React will receive data from Rails, we start by reviewing how Views are currently rendered to Users in Rails. As the below diagram demonstrates:
- a request is routed to the Controller via `routes.rb`
- the Controller requests data from the Model and provides it to the View
- the View generates HTML which Rails sends to the user

![](/assets/images/normal_rails_rendering_flow.png)

When using React for the frontend, a 2-step process is used to render the data to the user.
1. The React page is served to the user
1. The React page populates itself with data request from the Rails backend

While this may look inefficient, it is the best practice as it enables a clean, loosely-coupled interface between the frontend and backend. It also allows optimizations such as caching of the API data, instead of loading the data fresh every time a page is requested by the user.

{: .info}
> Note that these diagrams describe best practice. In some cases, it may be necessary to provide data to React component directly from the view - this is discussed in "Use Rails data serialization"

![](/assets/images/basic_react_rails_rendering.png)

The previous diagram demonstrated a situation where no specific information about the user's request was needed for the API call - the ListComps React component will make the same API request every time. In many cases, however, the user will be trying to view specific data about a particular Model object. In this case, the `id` of the data the user is requesting is provided to the React component during Step 1, which it will use to make an API call for a specific resource in Step 2.

![](/assets/images/variable_react_rails_rendering.png)

# Sourcing the data for your React component

As shown above, the goal is for your React app to receive a JSON payload containing the information it is looking for, ideally via an API call. That API _may_ exist, and if it doesn't, you _may_ be able to expose an endpoint for the data you need.

These steps should be worked through in order, to prevent unnecessary effort or inefficient solutions. They are explained in detail below.
- **Find existing API endpoints**: Look at `routes.rb` for existing API endpoints and query them to see what data they provide
- **Use WCIF**: If no endpoint exists, and your component is related to a competition, check if your data exists in WCIF. 
- **Create a new endpoint**: If you can't use WCIF (data doesn't relate to a competition, or WCIF is too big/slow), try and create a new API endpoint
- **Use Rails data serialization**: If you can't create your own endpoint, you'll need to use [Rails data serialization].

## Find existing API endpoints 

1. Find/open [routes.rb](https://github.com/thewca/worldcubeassociation.org/blob/master/WcaOnRails/config/routes.rb)
2. Search for `namespace :api do` - everything indented under this line defines an API endpoint
    1. Each `namespace` or `resources` name adds to the URL string. For example to access the `/me` endpoint under `namespace :api` and `namespace :v0`, use `/api/v0/me`
    1. For resources, be sure to include the resource ID. Example: `get /wcif` under `resources: competitions` becomes `/api/v0/competitions/{competition_id}/wcif`
        1. `competition_id` is found in the URL of a competition - `wc2023` is the `competition_id` of "https://www.worldcubeassociation.org/competitions/wc2023"
    1. `api.worldcubeassociation.org` is the base URL for API requests
1. Once you have an endpoint you want to query, send a request to it and inspect the payload to see if it contains the information you want
    1. In some cases, endpoints may require authentication - in this case you can inspect the controller function, run the repo locally, or contact WST
    1. Requests can be sent using `curl`, or an API resource like `Postman`
1. If the endpoint doesn't contain the data you want, but you think it should (eg, `/competitions` doesn't contain the `venue` field), you may suggest adding that field to the API output

## Use WCIF

1. Request WCIF for a competition with `api.worldcubeassociation.org/api/v0/competitions/{competition_id}/wcif`
2. Inspect the WCIF payload to see if it contains the field you're looking for

{: .warning}
> WCIF tends to produce large files which are slow to render. If you find that using WCIF leads to issues, you should consider using a dedicated API endpoint instead.

## Create a new endpoint

1. Consider whether the endpoint should be internal or public, and what data it should return
1. Create a new route under the `namespace :api do` section in `routes.rb`, and specify a controller function for it (refer to other API endpoints for guidance)
1. Write the controller function which renders the JSON you want the API to return

More detail is outside the scope of this documentation at present. Please consult WST for further help. 

## Use Rails data serialization

By default, Rails makes instance variables created in the Controller available to Views. We can make use of this to pass data (in the form of instance variables) to the `react_component` function.

`react_component` cannot understand the data in a Rails instance variable, because it is in a Rails-specific format. The data must be converted ("serialized") from this Rails-specific format to JSON so that `react_component` can understand it. For this, the `serializable_hash` method is invoked[^1]

If `serializable_hash` doesn't produce data in the format you require, you should either try and use `to_wcif`, or write a new method that converts your data into a JSON payload. `serializable_hash` **should not** be edited without a very good reason for editing it.

# Writing data to Rails

{: .info}
> Diagrams in this section are available for editing to WST members [here](https://app.diagrams.net/#G1V1mXsYcMw6cIaPJU3cA-s3pJ7psYovLD#%7B%22pageId%22%3A%22wrVvO56TNKLfE-PIFfzB%22%7D).

As seen below, writing data to Rails is very similar to the best practice for reading data from Rails. Essentially, **the Rails backend exposes an API endpoint which the React frontend can make POST/PATCH calls to.**

![](/assets/images/writing_data_react_rails.png)

The process of finding an API endpoint to write data to is the same as the previous section:
- Check if the endpoint already exists
- If yes, modify it if necessary and make use of it
- If no, create one (see "Create a new endpoint" for details)

----

[^1]: `serializable_hash` can serialize Rails data to formats other than JSON, but for our purposes we will only focus on the JSON serialization.
