# ASP.NET Core Instrumentation for OpenTelemetry .NET 

It is a simple project created with ASP.NET Core that uses OpenTelemetry for instrumentation and telemetry collection on incoming web requests.

## Getting started

Run Jaeger

>  docker-compose up -d

Start the service

> dotnet run

## Advanced configuration

This instrumentation can be configured to change the default behavior 

### Enrich

For this sample we can enrich with tags some very important **Headers["X-TENANT-ID"]**

```csharp
services.AddOpenTelemetryTracing((builder) => builder
                   .SetResourceBuilder(ResourceBuilder.CreateDefault().AddService(Configuration.GetValue<string>("Jaeger:ServiceName")))
                    .AddAspNetCoreInstrumentation((options) => options.Enrich = (activity, eventName, rawObject) =>
                    {
                        if (eventName.Equals("OnStartActivity"))
                        {
                            if (rawObject is HttpRequest httpRequest)
                            {
                                activity.SetTag("X-TENANT-ID", httpRequest.Headers["X-TENANT-ID"]);
                            }
                        }
                    })
                    .AddJaegerExporter(jaegerOptions =>
                    {
                        jaegerOptions.AgentHost = Configuration.GetValue<string>("Jaeger:Host");
                        jaegerOptions.AgentPort = Configuration.GetValue<int>("Jaeger:Port");
                    }));
```

## Exporter libraries

I used Jaeger, but there are many others, see the [OpenTelemetry registry](https://opentelemetry.io/registry/?s=net) for more exporters.

