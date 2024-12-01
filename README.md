### Adding custom header to CompleteChat using Azure.AI.OpenAI 2.0.0 in c#

This is how to add custom header using Azure.AI 2.0.0 SDK with C#:

First of all we should add new class that will inheritance `PipelinePolicy`.

`PipelinePolicy` is a type of `System.ClientModel.Primitives`, we will have to override some of its functions as below:



```
using System.ClientModel.Primitives;

public class CustomHeaderPolicy : PipelinePolicy
{
    private readonly string _headerName;
    private readonly string _headerValue;

    public CustomHeaderPolicy(string headerName, string headerValue)
    {
        _headerName = headerName ?? throw new ArgumentNullException(nameof(headerName));
        _headerValue = headerValue ?? throw new ArgumentNullException(nameof(headerValue));
    }

    public override void Process(PipelineMessage message, IReadOnlyList<PipelinePolicy> pipeline, int currentIndex)
    {
        message.Request.Headers.Add(_headerName, _headerValue);
        ProcessNext(message, pipeline, currentIndex);
    }

    public override ValueTask ProcessAsync(PipelineMessage message, IReadOnlyList<PipelinePolicy> pipeline, int currentIndex)
    {
        message.Request.Headers.Add(_headerName, _headerValue);
        return ProcessNextAsync(message, pipeline, currentIndex);
    }

}
```
To add the `CustomHeaderPolicy` to the request:

```
var options = new OpenAIClientOptions();

//this is important
options.Transport=  new System.ClientModel.Primitives.HttpClientPipelineTransport(new HttpClient());


//adding your custom header
options.AddPolicy(new CustomHeaderPolicy("key", apiKey), PipelinePosition.PerCall);

```


