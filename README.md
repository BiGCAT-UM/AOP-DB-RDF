# AOP-DB-RDF
This repository contains instructions to set up a SPARQL endpoint with AOP-DB RDF locally or on server (linux). Also, it contains the Jupyter notebook used to illustrate the functionality of the SPARQL endpoint in the corresponding manuscript.


## Set up a Virtuoso SPARQL endpoint with AOP-DB RDF (on linux):

### Step 1 - Create folder to mount
Enter the terminal and create a local folder to map to the docker container. Note the path to the folder to enter it at step 3. In this example, the folder '/aopdbrdf' was created and entered it by using:
```
mkdir -p aopdbrdf
```

### Step 2 - Move the RDF (.ttl) file into the newly created folder

### Step 3 - Run the Docker image
Be sure to map ports :8890 and :1111. In this case, the container was named "AOPDB". Also, this step configures the mapped local folder with the data, which is in this example "/aopdbrdf". The Docker image used is openlink/virtuoso-opensource-7. Run the Docker image by entering:
```
docker run -d --env DBA_PASSWORD=[ENTER PASSWORD] -p 8890:8890 -p 1111:1111 --name AOPDB --volume `pwd`/aopdbrdf/:/database/data/  openlink/virtuoso-opensource-7
```

### Step 4 - Enter the running container
The SPARQL endpoint should already be accessible through [localhost:8890/sparql/](http://localhost:8890/sparql/). However, while the Docker image is running, the data is not yet loaded. Therefore you need to enter the it by using:
```
docker exec -it AOPDB bash
```

### Step 5 - Move the .ttl files
First, copy the Turtle file(s) to the folder that is entered (the RDF riles are in the `/data` folder):
```
cp data/AOPDBRDF.ttl .
```
Then, exit the container:
```
exit
```

### Step 6 - Enter the container SQL and reset
Enter the running docker container SQL by using: 
```
docker exec -i AOPDB isql 1111
```
In case the service is already active and contains older RDF, be sure to perform a global reset and delete the old RDF files from the load_list, using the following commands. `[graph name]` is the name you called the graph when loading (step 7) if done previously:
```
RDF_GLOBAL_RESET();
DELETE FROM load_list WHERE ll_graph = '[graph name]';
```
The presence of files in the load_list can be viewed using the following command:
```
select * from DB.DBA.load_list;
```

### Step 7 - Load the RDF
Use the following commands to complete the loading of RDF. If errors occur, try again within a few seconds (which often works), or look at http://docs.openlinksw.com/virtuoso/errorcodes/ to find out what they mean. 
```
log_enable(2);
DB.DBA.XML_SET_NS_DECL ('aop.events', 'http://identifiers.org/aop.events/',2);
DB.DBA.XML_SET_NS_DECL ('aopo', 'http://aopkb.org/aop_ontology#',2);
DB.DBA.XML_SET_NS_DECL ('assay', 'https://comptox.epa.gov/dashboard/assay_endpoints/',2);
DB.DBA.XML_SET_NS_DECL ('bao', 'http://www.bioassayontology.org/bao#',2);
DB.DBA.XML_SET_NS_DECL ('cas', 'https://identifiers.org/cas:',2);
DB.DBA.XML_SET_NS_DECL ('cheminf', 'http://semanticscience.org/resource/CHEMINF_',2);
DB.DBA.XML_SET_NS_DECL ('chemicalgeneassociation', 'http://example.org/diseaseGeneAssociation',2);
DB.DBA.XML_SET_NS_DECL ('dc', 'http://purl.org/dc/elements/1.1/',2);
DB.DBA.XML_SET_NS_DECL ('edam', 'http://edamontology.org/',2);
DB.DBA.XML_SET_NS_DECL ('ensembl', 'http://identifiers.org/ensembl/',2);
DB.DBA.XML_SET_NS_DECL ('foaf', 'http://xmlns.com/foaf/0.1/',2);
DB.DBA.XML_SET_NS_DECL ('hgnc', 'https://identifiers.org/hgnc/',2);
DB.DBA.XML_SET_NS_DECL ('loinc', 'http://purl.bioontology.org/ontology/LNC/',2);
DB.DBA.XML_SET_NS_DECL ('mesh', 'http://purl.org/commons/record/mesh/',2);
DB.DBA.XML_SET_NS_DECL ('mi', 'http://purl.obolibrary.org/obo/MI_',2);
DB.DBA.XML_SET_NS_DECL ('mmo', 'http://purl.obolibrary.org/obo/MMO_',2);
DB.DBA.XML_SET_NS_DECL ('ncbigene', 'https://identifiers.org/ncbigene/',2);
DB.DBA.XML_SET_NS_DECL ('ncbitaxon', 'http://purl.bioontology.org/ontology/NCBITAXON/',2);
DB.DBA.XML_SET_NS_DECL ('proteinInteraction', 'http://example/proteinInteraction',2);
DB.DBA.XML_SET_NS_DECL ('pw', 'http://purl.obolibrary.org/obo/PW_',2);
DB.DBA.XML_SET_NS_DECL ('rdfs', 'http://www.w3.org/2000/01/rdf-schema#',2);
DB.DBA.XML_SET_NS_DECL ('sio', 'http://semanticscience.org/resource/',2);
DB.DBA.XML_SET_NS_DECL ('skos', 'http://www.w3.org/2004/02/skos/core#',2);
DB.DBA.XML_SET_NS_DECL ('string.node', 'https://string-db.org/network/',2);
DB.DBA.XML_SET_NS_DECL ('uberon', 'http://purl.obolibrary.org/obo/UBERON_',2);
DB.DBA.XML_SET_NS_DECL ('uniprot', 'https://identifiers.org/uniprot/',2);
DB.DBA.XML_SET_NS_DECL ('kegg.pathway', 'https://identifiers.org/kegg.pathway/',2);
DB.DBA.XML_SET_NS_DECL ('pharmgkb.pathways', 'https://identifiers.org/pharmgkb.pathways/',2);
DB.DBA.XML_SET_NS_DECL ('smpdb', 'https://identifiers.org/smpdb/',2);
DB.DBA.XML_SET_NS_DECL ('wikipathways', 'https://identifiers.org/wikipathways/',2);
DB.DBA.XML_SET_NS_DECL ('biocyc', 'https://identifiers.org/biocyc/',2);
DB.DBA.XML_SET_NS_DECL ('biocarta.pathway', 'https://identifiers.org/biocarta.pathway/',2);
DB.DBA.XML_SET_NS_DECL ('reactome', 'https://identifiers.org/reactome/',2);
DB.DBA.XML_SET_NS_DECL ('pid.pathway', 'https://identifiers.org/pid.pathway/',2);
DB.DBA.XML_SET_NS_DECL ('netpath', 'http://netpath.org/pathways?path_id=',2);

log_enable(1);
grant select on "DB.DBA.SPARQL_SINV_2" to "SPARQL";
grant execute on "DB.DBA.SPARQL_SINV_IMP" to "SPARQL";

ld_dir('/import', 'AOPDBRDF.ttl', '[graph name]');
```

To finalize the loading of data, use:
```
rdf_loader_run();
```

Check the status and look if the Turtle file is loaded correctly by entering:
```
select * from DB.DBA.load_list;
```

If the "il_state" = 2, the loading is complete. If issues occurred in this step, have a look at http://vos.openlinksw.com/owiki/wiki/VOS/VirtBulkRDFLoader. 

Quit the SQL by entering:
```
quit;
```

### Step 8 - Enter the Virtuoso service with loaded AOP-DB RDF
The container is running with loaded RDF, available through http://localhost:8890, or enter the SPARQL endpoint directly through http://localhost:8890/sparql/.
