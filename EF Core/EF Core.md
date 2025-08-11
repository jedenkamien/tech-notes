## How to view logs sent by EF to the Database?
Just add this code at the end of the class inheriting from DbContext:

`protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder.LogTo(message => Debug.WriteLine(message));`

**But:** if you're running something from a test, remember that there might often be logger stubs, and you'd need to create a real one (it's mentioned on StackOverflow, but I haven't tried it).
Instead, you can create a simple controller that directly calls my method and there I set the queries I'm interested in, e.g.:

`var temp = _repository.Query<GrandPaEntity>().IgnoreAutoIncludes()
            	.Where(gpe => gpe.AssetId == 1).ToList();
        	var temp2 = _repository.Query<GrandPaEntity>()
            	.Where(gpe => gpe.AssetId == 1).ToList();
        	var temp3 = _repository.Query<GrandPaEntity>().Any(gpe => gpe.AssetId == 1);
        	return;`

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


### How the logs can look like
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


## Case study: An interesting behaviour - dealing with a copy of a fetched collection

```csharp
public async Task<IList<Chapters>> GetAllChaptersForABook(MessageWithBook message, CancellationToken cancellationToken)
    	{
        	var books = _repository
            	.Query<Books>()
            	.Where(alc => alc.Author.FullNameId == "local full name id").ToList();

        	var booksForAKey = books
            	.Where(tlc => tlc.AccessKey == "electronic access key").ToList();

        	foreach (var tlc in booksForAKey)
        	{
            	tlc.VFrom = 10;
            	tlc.VTo = 100;
        	}

        	await _repository.SaveChangesAsync(cancellationToken);
        	return books.SelectMany(tlc => tlc.Chapters!).ToList();
    	}
```

This surprisingly works, although this is only a copy (not stricte sensum) of the original collection fetched from the database



## Case study: How does IgnoreAutoIncludes seem not to work under test circumstances? How does EF remember former database queries results?
`MyGrandParentEntity` has a bunch of `t.Navigation(e => e.ChildEntity).AutoInclude().UsePropertyAccessMode(PropertyAccessMode.Property);` autoincludes in `DbContext`.

`var temp = repository.Query<MyGrandParentEntity>().First();
//repository.Detach(temp);
var temp2 = repository.Query<MyGrandParentEntity>().IgnoreAutoIncludes().First();
repository.Detach(temp2);`

Now `temp2` has children and grandchildren included.

`var temp = repository.Query<MyGrandParentEntity>().First();
repository.Detach(temp);
var temp2 = repository.Query<MyGrandParentEntity>().IgnoreAutoIncludes().First();
repository.Detach(temp2);`

Now `temp2` does not have children included.

So EF, when taking some Entity from Db, first checks whether the appropriate entity is not already there in memory - if so, it can effortlessly take it from memory. That's why `IgnoreAutoIncludes` does not succeed, but actually no additional effort is suffered indeed. Detaching from any downloaded entities stored in variables will eventually solve the problem.


## How to detach from a fetched entity?
`dbContext.Entry(entity).State = EntityState.Detached;`


## Left Join subquery vs ordinary Left Join
From what I read, EF prefers left join subquery instead of regular left join.

Supposedly, with small data sets, the latter is more efficient, but with large ones, the former is much better.


## DbContext - some basic considerations about relationships
`modelBuilder.Entity<DocumentHeader>()
   .HasOne<Document>(dh => dh.Document)
   .WithOne(d => d.DocumentHeader)
   .IsRequired(false)
   .HasForeignKey<DocumentHeader>(dh => dh.DocumentId)
   .OnDelete(DeleteBehavior.NoAction);`

In this case, `IsRequired(false)` refers to the fact that the Principal (`Document`) doesn't need to have a Dependent (`DocumentHeader`).

_"Configures whether this is a required relationship (i.e. whether the foreign key property(s) can be assigned null)."_

In this case, `OnDelete` refers to the action that should happen to the Dependent when the Principal is deleted.

## EF migrations not that obvious sometimes
The topic of object relationships in EF code first is complex.

You can configure many settings without complaint from EF at first, but then EF itself reports many errors and problems.

EF allows too much during migration creation, but when it comes to applying the migration, problems suddenly appear.

You might sometimes think that you thought you understood EF very well, but it appears that apparently not :)

## OnModelCreation and implicit conventions
We specify each column in the context, although it's not necessary.

The snapshot can figure out much.

The snapshot isn't based on the Context, but on the model.

DbSets are retrieved from the Context.

Originally, fluent API is only meant to override non-standard behaviors and relationships.

Putting everything there isn't a disaster, but the file grows.

If you don't mention something in `OnModelCreation`, it simply won't override standard conventions.

## Mixing model and persistence - unrecommended
Many projects also configure most things using attributes over properties in models.

I don't like that approach.

You're mixing the model with persistence.

