# Re-Index

`POST /metadata/{entityName}/reindex`

This will trigger a reindexing of any hidden fields defined by Lightblue.  Currently, this only includes `caseInsensitive` indexes.

## Performance
Depending on the size of the collection, the documents in it, the fields being updated, and the hardware in the system, the reindexing can generally process between 5000 and 10000 records per minute per field.  That is +/- **10 hours** for a 5 million record collection.

This process does not keep track of state, and therefore the system should be isolated for the duration of the reindexing.  If the reindixing is disrupted, the process will have to be restarted
