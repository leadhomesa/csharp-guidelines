## GraphQL Schema Design

> Read the [docs](https://graphql.org/learn/)
> Read the C# lib [docs](https://graphql-dotnet.github.io/docs/getting-started/introduction)

### Use data loader factories :red_circle:
> https://graphql-dotnet.github.io/docs/guides/dataloader

**Note** This rule is currently in flux - the data structure is being changed

Requests to data sources are batched and cached short-term to avoid possible N+1 situations when executing.

**Bad**
``` c#
Field<ResultGraphType>()
	.Name("getRelatedThing")
	.Resolve(_ => dataSource.GetThingAsync(_.Source.RelatedThingId); // WRONG, Directly call the source
```

**Good**
``` c#
Field<ResultGraphType>()
	.Name("getRelatedThing")
	.Resolve(_ => dataLoader.GetThingAsync(_.Source.RelatedThingId); // CORRECT, Custom Data Loader using IDataLoaderContextAccessor

// Loader implementation
public Task<Result> GetThingAsync(long id)
{
	return _dataLoaderContextAccessor.Context.GetOrAddBatchLoader<long, Result>("GetResultThing", async ids =>
	{
		return await dataSource
			.Get(ids)
			.ToDictionary(g => g.Id, g => g.FirstOrDefault());
	}).LoadAsync(id);
}
```

### Inline Resolve implementations :large_blue_circle:
If the `Resolve` call becomes to busy, it is probably an indication that it is doing much more than it should.

``` c#
Field<ListGraphType<ResultGraphType>, ResultType>()
	.Name("getAll")
	.ResolveAsync(_ => dataSource.GetAllAsync());
```

**Exception** Create private methods suffixed with `Mock` when building mock data for a GQL response

### Create an Input class when the number of arguments for a method exceeds 2 items :large_blue_circle:
``` c#
Field<ListGraphType<ResultGraphType>, ResultType>()
	.Name("get")
	.Argument<FilterInputGraphType>("filter", "The filter")
	.ResolveAsync(async _ => {
		var filter = _.GetArgument<Filter>("filter");
		return await dataSource.Get(filter);
	});
```

### Folder and namespace structure :large_blue_circle:
:police_car: Review

``` c#
namespace Leadhome.Crm.Api.GraphQl.Types.<entity-type>.<type>
```

Given an entity, `Person`

`Leadhome.Crm.Api.GraphQl.Types.People`
- PersonMutationsGraphType
- PersonPatchMutationsGraphType
- PersonQueryGraphType
- PersonArea

`Leadhome.Crm.Api.GraphQl.Types.People.Loaders`
- PersonLoaderFactory

`Leadhome.Crm.Api.GraphQl.Types.People.Models`
- PersonGraphType

`Leadhome.Crm.Api.GraphQl.Types.People.Errors`
- DuplicateEmailException

### Do not suffic enum properties :large_blue_circle:
The type can be inferred from the schema generated. There should be no reason to explicitly name a property as `StatusType` or `StatusEnum` or `StatusOption`.