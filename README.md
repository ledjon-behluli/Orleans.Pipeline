# Orleans.Pipeline
Orleans.Pipeline is a library that adds a 2-way communication pipeline to Orleans.

---
## HowTo setup

### Client
Install the client package
```bash
dotnet add package Orleans.Pipeline.Client
```

Call the extension method `AddOrleansPipeline` on the `IHostApplicationBuilder` instance to add the pipeline to the client.
```csharp
builder.AddOrleansPipeline();
```

How to use the pipeline:
```csharp
//Retrieve a pipeline from `IOrleansPipelineClient`.
var client = serviceProvider.GetRequiredService<IOrleansPipelineClient>();
var pipeline = client.GetPipeline<string, string>("The id of this pipeline/ server side grain");

//Start the pipeline
await pipeline.Start(CancellationToken.None);

//Read from the pipelines Reader
await foreach (var result in pipe.Reader.ReadAllAsync(CancellationToken.None))
{
    //handle all received messages
}

//Write into the pipelines Writer
await pipeline.Writer.WriteAsync("Hello World!", CancellationToken.None);

//Stop the pipeline
await pipeline.Stop(CancellationToken.None);
```



### Silo

Install the server package
```bash
dotnet add package Orleans.Pipeline.Server
```

In your Grain inherit from `OrleansPipelineGrain<TToServer, TFromServer>`. Override `OnData(TToServer data)` to handle the data a client sent to this grain.

If you want to send data back to the client, call `Notify(TFromServer data)`.
```csharp
public class TestGrain(ILogger<TestGrain> logger) : OrleansPipeGrain<string, string>(logger)
{
    override protected Task OnData(string data)
    {
        logger.LogInformation("Received {data}", data);
        return Notify("I got your message!");
    }
}
```


---
## Contributing
Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.

