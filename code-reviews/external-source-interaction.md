## External Source Interaction

### Review checklist

1. EF call query is present in the code review description (we need to see what the query looks like)
2. Performance stats are present for the query in question (we need to know that the performance is generally good)
3. If we are renaming or removing a column there first needs to be a commit introducing the new replacement field, then a commit to remove the deprecated field. This reduces possible issue in the production environment due to properties suddenly going missing.