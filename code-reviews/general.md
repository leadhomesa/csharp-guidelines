## General Code Review Guidelines

### Keep pull requests as small as possible

- A pull request should ideally contain a stand-alone piece of logic that can be deployed to production immediately without any side-effects.
- The code review should be small enough to be reviewable in a few minutes (or less) with enough evidence as part of the PR description to prove that the change works as expected.

#### For schemas/skeletons/mocks

1. Add the minimal necessary logic - if only the skeleton is sufficient then stick to that and ensure that this skeleton can be queried against successfully
1. Add mocks relevant to what is being tested and share these with the party whom will be consuming/calling this skeleton so that they can test successfully
1. Schema changes that change property names or remove properties should be done in 2 phases:
	1. Introduce the new property and complete the PR and deployment
	1. Remove the property from the schema (once all new systems using the API have been deployed) and PR and deploy this change

#### For logic or query-logic

1. Attempt to stick to introducing 1 new piece of functionality per pull-request. That is, if you are allowing a new entity to be created, make that 1 PR - don't dump Create/Patch/Getters/etc. into a single PR.
	> An exception could be made if the logic is very simple - use your own discretion when deciding "how easy would this be to review?"
1. Add unit tests to test the logic introduced
1. Note the test cases in your pull request in plain english so we know which scenarios have been tested

**Note** The above scenario is an ideal and mostly applies to new functionality. Dependening on the extent of the spaghetti you were dealing with, a PR for a refactor can get quite hairy. In these cases, try and keep any additional changes minimal or properly point out any changes that you made and why.

#### For database models

1. Database model updates should be separate pull requests
1. When renaming or removing any tables or fields, or altering data types, deploy the change in 2 phases:
	1. Introduce the change as a new field or table, generate the Migration, and complete the PR and deployment
	1. Remove the table/field and PR and deploy this change

### Link pull requests to the source of the change and related changes

- Always drop a link back to the task you were working on into the description of the pull-request.
- If you are creating a PR and there is a related PR for it for another solution (for example, new addition on an API and changes for it in the corresponding front-end project), link to the PR to make it easy to navigate and see the progress of both - keeping in mind that this should ideally still be deployable without depending on the related PRs where possible.
- If this PR has a hard dependency on another, be very clear about this in the description and explain why.
