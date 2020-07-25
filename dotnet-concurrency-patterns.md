# Concurrency Patterns

Here is some patterns I use in the case of creation concurrent application. All samples will written be on C#, but I believe you can simply adopt them to java or any your preferred langname
> complete and working samples you can find on my [github](https://github.com/ondator/dotnet-concurrency-samples)

## Event Loop
Sometimes your possibly syncronious business logic should react to long-running process with some async logic(websocket or UDP receiving for example). In this case you can just create some kind of endless loop where you will process your async tasks and than call sync handle on result  

```cs
public interface SyncHandler
{
    public void Handle();
}

public class RemoteBackendClient
{
    private readonly SyncHandler _handler;
    public RemoteBackendClient(SyncHandler handler)
    {
        _handler = handler;
        _ = Task.Run(EndlessLoop);
    }

    private Task EndlessLoop()
    {
        while(true)
        {
            //do some async work here
            _handler.Handle();
        }
    }
}
```

> I believe that OS and browser event loop works quite similar to this approach, so I called this section event loop

> I know that this is not a best solution to refer a business logic from infrastructure code, but here is just a sample code of event loop approach, so I'm sorry for this. In production systems it will be better to use (for example) Rx to provide event stream interface from `RemoteBackendClient`

> Also there are no support for cancelation and no proper resources disposing. You should care about all of this in your production code

## Background Queue
Sometimes your pure(yep, asynchrony is side-effect too) business logic kernel should call async-IO API. For example if your business logic control persistence or any 3rd-party API calls. In this case you can simply connect them throught the queue

```cs
public class MySyncDomainObject
{
    public void DoSomeStuff(RemoteBackendClient client)
    {
        client.CallApi("hello!");
    }
}

public class RemoteBackendClient
{
    private readonly BlockingCollection<string> _queue = new BlockingCollection<string>();

    public RemoteBackendClient()
    {        
        _ = Task.Run(EndlessLoop);
    }
    private async Task EndlessLoop()
    {
        while(!_queue.IsCompleted)
        {
            if (_queue.TryTake(out var item, 1000))
            {
                await CallRemoteApi(item);
            }
        }
    }
    /// <summary>
    /// here should be real API call
    /// </summary>
    private Task CallRemoteApi(string message)
    {
        throw new NotImplementedException();
    }
    /// <summary>
    /// just add message to queue
    /// </summary>
    public void CallApi(string message)
    {
        _queue.Add(message);
    }
}
```
## Task Completion Source
Ok, now we can work with fire-n-forget scenario! But what with the case when you need to deal with request-response pattern, but you have no API out of the box? 
Good news! You can easely implement it by yourself with  `Task Completion Source`. 

Here we try to implement such API for [WAMP](https://wamp-proto.org/)-like RPC protocol, but you can see many use cases of this scenario from working with Kafka to UDP



## Spin Wait
Ok, now we can deal with IO-bound work, but how about CPU-bound? Sometimes(very rare, in my practice, but who knows) you need to make some intensive CPU-bound work in background. If you just run it in separate thread you can affect your main thread performance and(especially if you are hosted in clouds) your service SLI may degradate. Here is where `spin waiting` comes to help
> please be carefull with spin waiting. In fact it [isn't really a silver bullet](https://travisdowns.github.io/blog/2020/07/06/concurrency-costs.html) and may be you can find better solution in your special case

In my example I'll emulate CPU-intencive work with just an endless loop:

## Other
Actor model, CSP