# Refrences:

[Google drive notes for solr](https://drive.google.com/drive/folders/1HS5qOLq2hbaNNcxnkMV75gSgE6TovFeL?usp=share_link)

# Solr:

- `` solr stop -p portNo`` to stop, ``solr start -f`` starts at foreground, ``solr start -p portNo -e collectionName`` starts solr at background,```solr create -c coreName``` to create core,```./solr start -e cloud``` to start solr via linux command
-```solr.xml``` configuration options/settings for solr,```core.properties``` tells core name,schema location.

- ```schema.xml``` tells documents that solr will index and defines how indexes should be created from source documents,field data(chars,numbers)
- ```solrconfig.xml``` set config values for individual collections . Define alternate location for data dirctory,manage HTTP communications,cache configs,listen to particular query event and to define event based triggers.

  ![Solr Deployment Architecture.jpg](solrDeployArchi.PNG)

![Apache Solr Architecture.jpg](apachesolrarchi.PNG "Apache Solr Architecture")

## Solr configuration 
Reference:

 [Course Reference](https://vuram.percipio.com/courses/7daf4ab0-52ce-11e7-ae20-ceb650f9130b/videos/7db08331-52ce-11e7-ae20-ceb650f9130b)

 [Configuration of tesseract-ocr with solr - youtube video](https://youtu.be/z6s_gfw5Wkg)

[configuration of tesseract with solr - reference blog](https://blog.thedigitalgroup.com/using-solr-and-tikaocr-to-search-text-inside-an-image)

[Config set create,delete using curl](https://solr.apache.org/guide/7_4/configsets-api.html)
- ```<dataDir>$(solr.data.dir;)</dataDir>``` for indexes,solr.standardDirectoryFactory for best implementation of current JVM,```solr.NRTCachingDirectoryFactory``` wraps solr.standardDirectoryFactory and caches small files in memory for better NRT performance.We can force implementation via <p style="color:red">solr.MMapDirectoryFactory, solr.NIOFSDirectoryfactory, solr.SimpleFSDirectoryFactory</p>
- ```<directoryFactory class="${solr.directoryFactory:solr.NRTCachingDirectoryFactory}" name="DirectoryFactory"/>``` default implementation is schemaCodecFactory(Lucene Index format).If choose to customize index format(eg:IndexWriter.addIndex(IndexReader)) better convert back to official format again
- ```<codecFactory class="solr.SchemaCodecFactory">``` has compression mode BEST_SPEED(default),BEST_COMPRESSION and ```<indexConfig>``` to index documents has <p style="color:red">maxFieldLength,maxTime(default:1000),writeLockTimeout(default:1000),Expert: enabling compound file use less file for index(default:false(solr),true(lucene)),useCompoundFile,ramBufferSizeMB</p>
- ```<updateHandler class="solr.DirectUpdateHandler2">```,```<updateLog><str name="dir">${solr.ulog.dir:}</str><int name="numVersionBuckets">${solr.ulog.numVersionBuckets:65536}</int></updateLog>```,```<autoCommit><maxTime>${solr.autoCommit.maxTime:15000}</maxTime><openSearcher>false</openSearcher></autoCommit>``` auto commits all, instead of enabling autocomit use "commitWithin" when adding documents.```<autoSoftCommit><maxTime>${solr.autoSoftCommit.maxTime:-1}</maxTime></autoSoftCommit>``` different between hard/soft commit is soft commit is faster and more near realtime friendly than hard commit and changes are visibile but does not ensure that data is synced to disk.
- ```<slowQueryThresholdMillis>-1</slowQueryThresholdMillis>``` has two caches LRUCache(synchronized linkedHashMap),fastLRUCache(concurrentHashMap,faster gets and slower puts in single threaded operation,faster than LRUCache when hit ratio of cache is >75%)
- ```filterCache``` used by solrIndexSearcher for filters,prepopulated or autowarmed. ```queryResultCache``` ordered lists of docIds based on query.```documentCache```  lucene internal document id are transient,this cache won't autowarmed.
- Natively supports indexing structured documents in XML,CSV,JSON formatted files.
- For POSTing to solr use bin/post on 'nix' systems or ```SimplePostTool``` in windows that is post.jar in example/exampledocs folder using <span style="color:red">java -jar post.jar -h</span>
- ```java -Dc=coreName -jar post.jar *.xml ``` to index solr with xml,```java -Dtype=application/json -Dc=coreName -jar post.jar *.json``` to index solr with json,```java -Dtype=text/csv -Dc=coreName -jar post.jar *.csv``` to index solr with csv  . If Dtype(DataType) is not specified XML is taken by default 
- ```solr create -c deleteCollection -n gettingstarted -p 8983``` and the syntax is `bin/solr create [-c name] [-d confdir] [-n configName] [-shards or -s #] [-replicationFactor or -rf #] [-p port]` is one way, ```http://localhost:8983/solr/admin/collections?action=CREATE&name=showcase&numShards=2&replicationFactor=2&maxShardsPerNode=-1&collection.configName=gettingstarted``` is second way, and third way is creating in UI
- ```solr delete -c deleteCollection -n gettingstarted -p 8983``` is one way to delete, ```http://localhost:8983/solr/admin/collections?action=DELETE&name=deleteCollection&numShards=2&replicationFactor=2&maxShardsPerNode=-1&collection.configName=gettingstarted``` is second way, third way is to change in UI 
-  ```java -Dauto -Dbasicauth=username:password -Durl="http://localhost:8983/solr/gettingstarted/update" -jar C:\Users\shankarr\Desktop\solr-8.11.0\example\exampledocs\post.jar C:\Users\shankarr\Desktop\solr-8.11.0\example\exampledocs\TechProducts``` to index the files present in that folder
- ```curl "https://collabora.vuram.com:8983/solr/techproducts/update?commit=true" -H "Content-Type: text/xml" --data-binary "<delete><query>*:*</query></delete>"``` to delete content,document from core
- ```curl "http://localhost:8983/solr/gettingstarted/select?wt=json&indent=true&q=*.*"``` to print all contents in collection, instead of q="*" set q=population=1 to get one content from collection.```fl``` is to get particular fields
![fl field](flFieldUse.PNG "Fl field's example")
- In `q` field if we define in "" it take as one single sentence and search that single in collection,if we define without "" it search by word by word then with sentence.
![Search Query](searchQuery.PNG "Search query how it works")
- If node is stopped at particular ports, use ```./bin/solr start -c -p <portNumber> -s example/cloud/node2/solr -z localhost:9983``` to start the node.
- Add or delete replica for particular shards by referring this [documentation](https://solr.apache.org/guide/8_2/replica-management.html#:~:text=be%20processed%20asynchronously.-,DELETEREPLICA%3A%20Delete%20a%20Replica,delete%20the%20instanceDir%20and%20dataDir.)
- Connect solr to zookeeper by default using the following [steps](https://docs.microfocus.com/doc/UCMDB/2023.05/SASolrCloud) before installing the solr, so it will be easier to stop,start or restart zookeeper.
- Search process by query parser(helps in search strings with index,set importance of particular strings or fields,apply boolean logic between search terms,excluding contents,hightlight)
-  ```title:"foo bar" AND body:"quick"``` will search both in same time, ```title="foo bar"&body="quick"``` gives those that contains one of these two,```content:*Sparrow* AND foo_txt:"started"``` we can use this to search both in OCR and contents.
![Filter Query](filterQuery.PNG "Filter Query example")
- DIH(Data Import Handler)used to import and index content from structured data store,has to be registered in ```solrconfig.xml```. Refer [here](https://vuram.percipio.com/courses/90b33102-5039-11e7-867a-028372024997/videos/90b33107-5039-11e7-867a-028372024997)
- ```java -Dauto -Durl="http://localhost:8983/solr/gettingstarted/update?wt=json&literal.id=d111&uprefix=attr_&fmap.content=attr_content&commit=true" -jar post.jar testImageSolr2.PNG``` to index image with tesseract-OCR .
- ```curl -X POST "http://localhost:8983/solr/gettingstarted/update/extract?literal.id=d112&literal.field1=testImageSolr.jpg&captureAttr=true&defaultfield=content&capture=div&fmap.div=foo_txt&boost.foo_txt=2&" -F "tutorial=@testDocForSolr.docx"``` gives both ocr and docx contents (need to check).[View here](https://stackoverflow.com/questions/9558526/indexing-multiple-documents-and-mapping-to-unique-solr-id)
### Facet Based Searching
Reference:[facets youtube video](https://youtu.be/msDPlY1SeDc)
- Facet based Searching,Number range facets,Field facets,Geo-spatial facets
- ```curl -X GET "http://localhost:8983/solr/TechProducts/select?q=memory&facet=true&facet.field=menu``` to see facet menu, ```curl -X GET "http://localhost:8983/solr/gettingstarted/select?q=memory&facet=true&facet.field=manu&facet.range=price&f.price.facet.range.start=0&f.price.facet.range.end=500&f.price.facet.range.gap=100"``` to filter in facets
- ```curl -X GET "http://localhost:8983/solr/gettingstarted/select?q=memory&facet=true&facet.field=price&fq=price:\[100%20TO%20200\]"``` to filter further in the selected facet field.
- ```http://localhost:8983/solr/gettingstarted/select?facet.field=content&facet.missing=false&facet.query=content%3Drealities&facet=true&indent=true&q.op=OR&q=content%3D%22augmented%22&rows=100``` example to search content in content
![facet search params](facetSearchParams.PNG "Facet search params fields")
![facet search params](facetSearchParams2.PNG "Facet search params fields")

### Security:
- `solr auth enable -type basicAuth -prompt true -zkHost localhost:9983 -blockUnknown true` to enable the basic authentication,`solr auth disable -type basicAuth -prompt true -zkHost localhost:9983 -blockUnknown false` to disable authentication.
- ```solr zk cp file:security.json zk:/security.json -z localhost:9983``` upload security.json file to zookeeper. To disable the authentication that is enabled by copying json to zookeepr set `basicAuthentication:false` else run ` ./bin/solr auth disable -type basicAuth -prompt true -z 127.0.0.1:9983`      

### Solr Data Model
- Analyzer examines text of fields and generates token. Eg:For textfields ```<analyzer class="org.apache.lcene.analysis.coreWhitespaceAnalyzer"```
![fieldTypes](fieldtypes.PNG "Field types of solr")
![supported field types](supportedFieldTypes.PNG "supported field types in solr")
![supported field types](supportedFieldTypes2.PNG "supported field types in solr").
![partial Search](solrPartialSearch.PNG "Solr Partial Search example xml")
![Query Parameters](queryParams.PNG "Query Parameters")
# How to optimize?
- The Optimize operation re-organizes all the Segments in a Core (or per shard) and merged them to 1 single Segment (default is 1 segment) To optimise: You could specify MergePolicy in solrconfig.xml, so that Solr will merge segments by himself
[Some Optimizing ways](https://www.searchstax.com/blog/5-ways-to-optimize-your-solr-search-performance-for-sitecore/)
# Others:
- Netflix uses solr,Now google search engine uses solr
- In solr each instance is node .In solr core copy of shard runs in node known as replica.Replica of shard is Leader that distributes requests of solr cloud to remaining replicas.
- Solr sharding involves splitting a single Solr index into multiple parts, which may be on different machines. When the data is too large for one node, you can break it up and store it in sections by creating one or more shards, each containing a unique slice of the index.
- [default field (uses)](https://stackoverflow.com/questions/17363677/solr-df-and-qf-explanation)
- ```AutoWarming``` is to get a large quantity of your most frequently accessed documents into the document caches so they don't have to be read from disk
- ```Router.field``` defines how documents will be distributed among the shards. Possible values are implicit or compositeId , which is the default. The implicit router does not automatically route documents to different shards.
- Each configuration is called a Solr core. Having multiple cores is useful when you want different applications or different versions of CKAN to share the same Solr instance, each application can have its own Solr core so each can use a different schema. xml file
- A Cluster is made up of one or more Solr Nodes, which are running instances of the Solr server process. Each Node can host multiple Cores. Each Core in a Cluster is a physical Replica for a logical Shard. Every Replica uses the same configuration specified for the Collection that it is a part of.
- A Solr replication master is a single node which receives all updates initially and keeps everything organized. Solr replication slave nodes receive no updates directly, instead all changes (such as inserts, updates, deletes, etc.) are made against the single master node.
- The replication factor, on the other hand, dictates the number of physical copies that each shard will have. So, when replication factor is set to 1, only leader shards will be created. ... By default, Solr will put one shard of a collection on a given node.
- The default is 20 . maxConnections. The total maximum number of concurrent connections in distributed searches. The default is 10000
- The escape characters that lucene supports in solr. Check [here](https://lucene.apache.org/core/3_6_0/queryparsersyntax.html#Escaping%20Special%20Characters)
- To further work with extra operators like AND, OR see [here](http://www.lucenetutorial.com/lucene-query-syntax.html)
- There is log4j Vulnerability in solr [Reference doc by apache](https://logging.apache.org/log4j/2.x/security.html),[Reference Video](https://youtu.be/lBxZL98uvdk),[Reference doc by docker](https://www.docker.com/blog/apache-log4j-2-cve-2021-44228/)
[When we should apply hard commit and soft commit in solr](https://stackoverflow.com/questions/45998804/when-should-we-apply-hard-commit-and-soft-commit-in-solr)

[Collection vs core](https://stackoverflow.com/questions/17044640/solr-collection-vs-cores)


# Best Practice to follow
[Best practice on shards](https://stackoverflow.com/questions/63961255/best-practice-on-number-of-shards-replicas-with-solr-cloud)


