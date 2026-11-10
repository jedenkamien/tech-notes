## tech-notes
A personal collection of engineering notes captured while investing real software development problems.

Unlike a structured knowledge base, this repository focuses on specific issues, surprising behaviors, debugging sessions and implementation details encountered during day-to-day work.

Topics are intentionally diverse and may range from Entity Framework internals to database behavior, application architecture, testing, Azure services or development tooling.

The goal is not to provide comprehensive documentation, but to preserve practical findings and interesting case studies that may be useful in future projects.

## Why this repository exists
Many of these notes originated while:
- debugging unexpected application behaviour,
- investigating framework internals, 
- validating assumptions about platform features,
- documenting lessons learned from real projects. 

The repository serves both as a personal engineering journal and as a collection of practical case studies.

-------------------------------------------------------------------------------------------------------------------------


-------------------------------------------------------------------------------------------------------------------------


-------------------------------------------------------------------------------------------------------------------------


## Example note:


### How to view logs sent by EF to the Database?
Just add this code at the end of the class inheriting from DbContext:

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder.LogTo(message => Debug.WriteLine(message));
```

**But:** if you're running something from a test, remember that there might often be logger stubs, and you'd need to create a real one (it's mentioned on StackOverflow, but I haven't tried it).
Instead, you can create a simple controller that directly calls my method and there I set the queries I'm interested in, e.g.:

```csharp
var temp = _repository.Query<GrandPaEntity>().IgnoreAutoIncludes()
            	.Where(gpe => gpe.AssetId == 1).ToList();
        	var temp2 = _repository.Query<GrandPaEntity>()
            	.Where(gpe => gpe.AssetId == 1).ToList();
        	var temp3 = _repository.Query<GrandPaEntity>().Any(gpe => gpe.AssetId == 1);
        	return;
```

The controller can be as simple as this:

```csharp
 [ApiController]
	`public class TestApiController : ControllerBase
	{
    		private readonly IMediator _mediator;
    		private readonly IMyService _myService;

    		public TestApiController(IMediator mediator, IMyService myService)
    		{
        		_mediator = mediator;
        		_myService = myService;
    		}

    		[HttpGet("DoSomething")]
    		public async Task DoSomething(long canBeNoArgumentAtAllInTheEnd)
    		{
        			await _myService.CheckDataAgainstAllowedLimits();
    		}
	}
 ```


#### How the logs can look like
Logs can be as simple as in the case of .Any():

```sql
	SELECT CASE
      	WHEN EXISTS (
          	SELECT 1
          	FROM MyTable AS [t]
          	WHERE [t].[SomethingId] = CAST(1 AS bigint)) THEN CAST(1 AS bit)
      	ELSE CAST(0 AS bit)
  	END
```

In the case of auto-includes or includes, they can have many joins and subqueries.