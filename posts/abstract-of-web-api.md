<!--
.. title: Abstract of Web API
.. slug: abstract-of-web-api
.. date: 2020-09-21 18:53:22 UTC+09:00
.. tags: Web API
.. category: RICHKA
.. link:
.. description:
.. type: text
.. author: Hiroki Murashige
-->

# Abstract

In this post, I will explain the mechanism of Web API provided by RICHKA.  
In addition to operations from a normal Web browser, RICHKA provides a Web API mechanism for use by access from   other than the browser for asynchronous processing, etc., and for future expansion.  
 
Due to the mechanism of WebAPI, it is possible to mainly use the following functions.
- Manipulation of specific data
- Asynchronous execution of video generation processing
- Webhook notification after the generation process is completed
 
(At the time of writing, it is still a beta version, it is used only internally, and it is not open to the outside.)  

# Basic usage

The Web API provided by RICHKA is implemented based on the REST format using the [Django Rest Framework](https://www.django-rest-framework.org/) and is easy to use.  

The Web APIs currently available include the following.

|type|explain|
|:--|:--|
|user|Acquisition of user information, authentication function, etc.|
|video data|Editing video data and generationg and projects, etc.|
|format|Reference of format information used for editing video data, etc.|
|webhook|Settings for webhook notifications|  
|||  
  
For example, the following is a Web API for operating projects in RICHKA.  
（Currently, it is mainly intended for internal use, so it is different from what is actually available.）  

|METHOD|URI|explain|
|:--|:--|:--|
|GET|https://xxxxxx/v1/projects/|Get a list of projects|
|GET|https://xxxxxx/v1/projects/id/|Get information about a specific project|
|POST|https://xxxxxx/v1/projects/|Create a project|
|PUT|https://xxxxxx/v1/projects/id/|Update project information|
|DELETE|https://xxxxxx/v1/projects/id/|Delete the project|
|||  
  
Each WebAPI is called using a pre-issued API token.  
If you call the Web API with the required parameters, the HTTP response status will be returned according to the result.  
The following is an example of calling the Web API that creates a project.   

```
curl -X POST \
      https://xxxxxx/api/v1/projects/ \
      -H 'Authorization: Token TOKEN_AUTH' \
      -H 'Content-Type: application/json' \
      -d '{
        "name": "New Project"
        }'

```

HTTP status 200 and the resulting JSON data will be returned.  

```
{
    "user_name":"xxxxx","name":"New Project","project_id":"yyyyyyyy","project_type":"","created_at":"2020-09-21T19:49:00.097424+09:00","updated_at":"2020-09-21T19:49:00.097460+09:00","status":"success"
}

```

# Design concept

RICHKA has designed a simple and extensible model with careful consideration of the dependencies and relationships of individual applications.  
Therefore, the implementation on the WebAPI side is relatively easy.  
It is built with the configuration of each class in the basic Django Rest Framework.  

```
views → Authentication, Throttle, Logging.
↓
serializers → Additional data processing etc.
↓
models
↓
backends → Video generation, webhook notification.

```

## views 

In each View, the authentication method, the limit on the number of calls by throttle, logging, etc. are mainly set.  
In addition, depending on the passed parameters, the processing may be slightly branched or simple value processing is performed.  

## serializers 

Basically, it is only converted by ModelSerializer, but there are some that process the result because it is returned by WebAPI.  

## models

There are several Web API-specific models for logging and limiting.  

## backends

Contains backend classes called from view.  
It mainly includes a wrapper for video generation processing and a webhook mechanism.  


# Example of using WebAPI for RICHKA

RICHKA and other related services have already begun to use some WebAPIs, but for example, users can generate videos completely asynchronously by calling the WebAPIs in the following flow.

```

User → Call authentication Web API → Web API Server
├─────────────────────────────────── ← ─┘
↓
│
├─ → Call Create Video Data Web API → Web API Server
├─────────────────────────────────── ← ─┘
↓
│
├─ → Call Generate Video Data Web API → Web API Server ─┐
│                                                       │
│                                                   Video Server → Generate Video
│                                                       │
├──────────────────────────── ← Web hook notification ←─┘
↓
│
:

```