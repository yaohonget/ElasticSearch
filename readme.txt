Relational DB -> Databases -> Tables -> Rows -> Columns
Elasticsearch -> Indices -> Types -> Documents -> Fields


shared -> primary shared / duplicated shard
node -> master node / normal node


Metadata
_index
_type
_id

#CREATE OR REINDEX/UPDATE
PUT /{index}/{type}/{id}
{
	"field" : "value",
	...

}

return
{
  "_index" :   "website",
  "_type" :    "blog",
  "_id" :      "123",
  "_version" : 2, // increase if update occurred
  "created":   false // UPDATE
}

PUT /website/blog/123?op_type=create
{ ... }
PUT /website/blog/123/_create
{ ... }
return 201
if exists return 409
{
  "error" : "DocumentAlreadyExistsException[[website][4] [blog][123]:
             document already exists]",
  "status" : 409
}

POST /{index}/{type}
{
	"field" : "value",
	...
}
increased URL-safe, base64-encoded string universally unique identifiers

POST /website/blog/1/_update
{
   "doc" : { // using doc parameter to add new fields
      "tags" : [ "testing" ],
      "views": 0
   }
}


#SEARCH
GET /{index}/{type}/{id}?pretty
curl -i -XGET http://localhost:9200/website/blog/124?pretty

search specific fields
GET /{index}/{type}/{id}?_source=field1,field2
GET /{index}/{type}/{id}/_source

check if exists
HEAD /{index}/{type}/{id}
curl -i -XHEAD http://localhost:9200/website/blog/123

multi get
/_mget

#DELETE
DELETE /website/blog/123
return
{
  "found" :    true,
  "_index" :   "website",
  "_type" :    "blog",
  "_id" :      "123",
  "_version" : 3 // would also increased
}
if not exists, return 
{
  "found" :    false,
  "_index" :   "website",
  "_type" :    "blog",
  "_id" :      "123",
  "_version" : 4
}
it will not be permanently removed from disk but only marked as deleted, only when new index created later, the existing as "deleted" marked record would be removed from disk.

#Bulk Operation
POST /_bulk
{ "delete": { "_index": "website", "_type": "blog", "_id": "123" }} 
{ "create": { "_index": "website", "_type": "blog", "_id": "123" }}
{ "title":    "My first blog post" }
{ "index":  { "_index": "website", "_type": "blog" }}
{ "title":    "My second blog post" }
{ "update": { "_index": "website", "_type": "blog", "_id": "123", "_retry_on_conflict" : 3} }
{ "doc" : {"title" : "My updated blog post"} } 
each line must be ended with "\n", include the last line.
