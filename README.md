# instapush.net
Instapush API unofficial .NET wrapper
> This client uses Instapush REST API v1 (https://api.instapush.im/v1/)

## Installation 
You can directly download from  [**Nuget**](https://www.nuget.org/packages/Instapush.Client.Rest) or 
in your Package Manager Console run "**Install-Package Instapush.Client.Rest**"

If you need more details about API methods, please look at [Instapush Developer - API page](https://instapush.im/developer/rest)

>Your feedbacks are welcome to emregulcan [a] gmail.com

## Usage
Instapush client;

Has 2 different methods;

1. "**Execute**" is for operations like Create, Update, Delete or Send notifications
2. "**Retrieve**" is for Select / query operations

Has 5 different messages for every single request;

1. CreateApplicationRequest
2. CreateEventRequest
3. CreatePushNotificationRequest
4. RetrieveApplicationListRequest
5. RetrieveEventListRequest

## Code Samples

### Instance Client

```C#
Instapush.Client.Rest.Client client = new Client.Rest.Client(new Client.Rest.ClientConfiguration() { Token = "YOUR USER TOKEN HERE"});
```

### Create an application
```C#
CreateApplicationRequest request = new CreateApplicationRequest()
{
    Title = "YOUR APPLICATION NAME/TITLE HERE"
};

var createdApplication = (ExecuteResponse<ApplicationModel>)client.Execute(request);
```

### Create an event (that related with an existing application)
```C#
CreateEventRequest request = new CreateEventRequest()
{
  ApplicationId = createdApplication.Item.Id,
  ApplicationSecret = createdApplication.Item.Secret,
  Title = "EVENT TITLE",
  Message = "EVENT MESSAGE (with parameters)"
};
var createdEvent = (ExecuteResponse<EventModel>)client.Execute(request);
```

> You can pass parameters (aka *trackers*) in your request.Message with curly brackets like "**_{username}_ login at _{date}_**"

### Create (Send) a push notification
```C#

List<KeyValuePair<string, string>> trackers = new List<KeyValuePair<string, string>>()
{
    new KeyValuePair<string, string>("username", "emregulcan"),
    new KeyValuePair<string, string>("date", DateTime.Now.ToString())
};

CreatePushNotificationRequest request = new CreatePushNotificationRequest()
{
    ApplicationId = createdApplication.Item.Id,
    ApplicationSecret = createdApplication.Item.Secret,
    Event = createdEvent.Item.Title,
    Parameters = trackers
};
var notification = (ExecuteResponse<PushNotificationModel>)client.Execute(request);
```

> When you send this request to API, your Instapush application receives "emregulcan login at ....." notification

### Retrieve existing application list
```C#
RetrieveApplicationListRequest request = new RetrieveApplicationListRequest() { };
var applicationList = (RetrieveResponse<ApplicationModel>) client.Retrieve(request);
```

### Retrieve existing event list (that related with an existing application)
```C#
RetrieveEventListRequest request = new RetrieveEventListRequest() 
{ 
  ApplicationId = "APPLICATION ID", 
  ApplicationSecret = "APPLICATION SECRET" 
};
var eventList = (RetrieveResponse<EventModel>)client.Retrieve(request);
```

## Exceptions
Client throws known-exceptions that listed below

### InvalidRequestException
When your request is invalid because of any reason

### InvalidUserTokenException
When your User Token is invalid

### InvalidApplicationIdOrSecretException
When your application ID or Secret Key is invalid

### DuplicateRecordException
When event (name) is already exists in your application

### ParameterNotExistsException
When your parameters (tracker) not included in your request

### RecordNotExistsException
When your event (name) not exists in (or not related with) application
