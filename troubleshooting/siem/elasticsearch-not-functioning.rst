Elasticsearch is not functioning properly
-----------------------------------------

When Elasticsearch or Kibana are not working, there can be a number of
potential causes. The following checks, conducted with Elasticsearch’s
REST API mostly via NetEye’s **es_curl** script, can help you diagnose the
problem.

1. The first thing to do is to make sure that the `cluster is running properly
   <https://www.elastic.co/guide/en/elasticsearch/reference/current/cluster-health.html>`_
   overall. You can do this by checking that the output of this command
   contains **status: green:**::

    # /usr/share/neteye/elasticsearch/scripts/es_curl.sh -X GET "https://elasticsearch.neteyelocal:9200/_cluster/health" | jq .

2. If there is a problem with the connection between cluster nodes
   (e.g., network or certificate issues), they will not be able to
   carry out tasks that require communication. This command lists all
   those cluster nodes that have `joined the Elasticsearch cluster
   <https://www.elastic.co/guide/en/elasticsearch/reference/current/cat-nodes.html>`_. All
   of the cluster nodes should be included in the list.

  ::

     # /usr/share/neteye/elasticsearch/scripts/es_curl.sh -X GET "https://elasticsearch.neteyelocal:9200/_cat/nodes?v"

3. When a cluster is not working well, often the cause is an `index
   <https://www.elastic.co/blog/what-is-an-elasticsearch-index>`_ in
   poor health (i.e., one marked with a “yellow” or “red” status,
   derived from the worst status of the index’s shards). In this case,
   there is a real risk of losing data if something goes wrong. You can
   find `exactly which indices are problematic
   <https://www.elastic.co/guide/en/elasticsearch/reference/current/cat-indices.html>`_
   with this command::

    # /usr/share/neteye/elasticsearch/scripts/es_curl.sh -s -X GET "https://elasticsearch.neteyelocal:9200/_cat/indices?v"

4. Shards contain the actual data in an Elasticsearch cluster, and can
   be relocated to or replicated in different cluster nodes. Like
   indices, the existence of problematic shards can be an important
   reason why a cluster is not working properly. You can check the
   status using the following command. Since there may be a large
   number of shards, you can find `filtering and sorting options
   <https://www.elastic.co/guide/en/elasticsearch/reference/current/cat-shards.html>`_
   in the Elastisearch documentation.

  ::

     # /usr/share/neteye/elasticsearch/scripts/es_curl.sh -X GET "https://elasticsearch.neteyelocal:9200/_cat/shards?v"

5. Finally, the cluster may be in perfect health, but you may not be
   able to visualize any data because the Kibana module is down. You
   can check it is `properly functioning
   <https://www.elastic.co/guide/en/kibana/current/access.html>`_ by
   checking that the node with id marked “plugin:kibana” has state:
   green in the output of this command::

    # curl -X GET "http://kibana.neteyelocal:5601/api/status" | jq -r '.status.statuses[]'
