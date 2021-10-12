# Extending RawRabbit
`RawRabbit` provides a solid foundation for reliable request/reply and publish/subscribe operations. In addition to this, [`RawRabbit.Extensions`](https://www.nuget.org/packages/RawRabbit.Extensions/) can be used to write extensions to the client, making it possilbe to customize the client for any specific needs. The extension framework exposes a method for resolving registered `RawRabbit` internal services.

## Installation
Install the latest version of `RawRabbit.Extensions` from NuGet.

```nuget

  PM> Install-Package RawRabbit.Extensions

```

## The Extendable Bus Client

The `ExtendableBusClient` is an super class of the normal bus client, that exposes the method `GetService<TService>` (which is just a wrapper around [`Microsoft.Extensions.DependencyInjection.IServiceProvider`](https://www.nuget.org/packages/Microsoft.Extensions.DependencyInjection.Abstractions/)). This method allows you to resolve the registered services that `RawRabbit` uses. This way, if you for example has a custom `IContextProvider` that you need to get a hold of, it's just a call away.

## Extension boilerplate

```csharp
public static class RawRabbitExtensionExample
{
	public static void DoStuff<TContext>(this IBusClient<TContext> client)
		where TContext : IMessageContext
	{
		var extended = (client as ExtendableBusClient<TMessageContext>);
		if (extended == null)
		{
			//TODO: nice error handling
			throw new InvalidOperationException("");
		}
		var channel = extended.GetService<IChannelFactory>().CreateChannel();
		// resolve stuff, make calls...
	}
}
```

## List of extensions
* The [BulkGet extension](Bulk-fetching-message.html) can be used to fetch multiple messages from multiple queues and `ACK`/`NACK` them in bulk.
* The [Message Sequence Extension](message-sequence.html) can be used to get a RPC like behaviour, but allows for multiple subscribers to act on the message
* The [Update Topology Extension](update-topology.html) can be used to change topologic features.
