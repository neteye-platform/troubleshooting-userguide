Managing an Elasticsearch Cluster with a Full Disk
--------------------------------------------------

If a cluster node’s disk `begins to fill up
<https://www.elastic.co/guide/en/elasticsearch/reference/current/disk-allocator.html>`_
(defined as going below a low disk watermark, with the value for
*cluster.routing.allocation.disk.watermark.low* set by default to 85%),
then replicas will no longer be assigned to that node by setting the
flag *read_only_allow_delete*. This may cause unexpected behavior
because new indices and shards may not be allocated or replicated
correctly.

Once more space is available for the Elasticsearch data (e.g., you have
increased the available space for the logical volume on the cluster),
you should reset the value to normal to allow the node to return to
normal function by using the following command::

  # /usr/share/neteye/elasticsearch/scripts/es_curl.sh 'https://elasticsearch.neteyelocal:9200/*/_settings' -X PUT -H 'Content-Type: application/json' -d'
       {
         "index.blocks.read_only_allow_delete": null
       }
       ' | jq .
