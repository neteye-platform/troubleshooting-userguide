Some logs are not indexed in Elasticsearch
------------------------------------------


It might happen that some log files collected by the NetEye Log
Manager module are not indexed correctly or not indexed at all in
Elasticsearch.  These logs can be manually reindexed in Elasticsearch
via the script ``elasticsearch-reindex-logs`` that can be found under
``/usr/share/neteye/backup/elasticsearch/``

The script can be run by typing::

  sh elasticsearch-reindex-logs -f /full/path/to/logfile.log.gz

The input must be a log file that has been previously gzipped by the
Log Manager module.  The full set of options is displayed by running
the script as follows::

  sh elasticsearch-reindex-logs --help

Pay attention to avoid to use the script to reindex a log that is
already indexed in Elasticsearch. This causes the duplication of the
same data in Elastisearch.
