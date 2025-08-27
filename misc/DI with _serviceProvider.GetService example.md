## Factory with serviceProvider.GetService to choose between 2 implementations, adhering to DI principles

```csharp

/// <summary>
/// Factory to initialize the correct <seealso cref="IDocumentService"/> implementation based on the tenant's configuration
/// </summary>
public class DocumentServiceFactory : IDocumentServiceFactory
{
	private readonly IServiceProvider _serviceProvider;
	private readonly IConfigCacheAccessor _configCacheAccessor;
    
	/// <summary>
	/// Factory to initialize the correct <seealso cref="IDocumentService"/> implementation based on the tenant's configuration
	/// </summary>
	/// <param name="serviceProvider">The service provider for resolving the required dependencies</param>
	/// <param name="configCacheAccessor">The config cache accessor for reading the tenant's configuration</param>
	public DriftLogicFactory(IServiceProvider serviceProvider, IConfigCacheAccessor configCacheAccessor)
	{
    	_serviceProvider = serviceProvider;
    	_configCacheAccessor = configCacheAccessor;
	}
    
	/// <summary>
	/// Initializes the correct <see cref="IDocumentService"/>/> instance based on the tenant's configuration
	/// </summary>
	/// <returns>The initialized <see cref="IDocumentService"/> instance.</returns>
	public IDocumentService CreateDocumentService()
	{
    	var oneParameter = _configCacheAccessor.GetLatestConfigCacheEntry()
        	.ConfigurationAccess.GetSetting(OneSetting.VALUE_OF_A_PARAMETER);

    	if (string.IsNullOrWhiteSpace(oneParameter))
        	return (IDocumentService) _serviceProvider.GetService(typeof(DocumentService));

    	return (IDocumentService) _serviceProvider.GetService(typeof(EnrichedDocumentService));
	}
}


```
