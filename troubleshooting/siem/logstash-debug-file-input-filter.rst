Debugging Logstash file input filter
------------------------------------

Debugging if a file is parsed or not by Logstash is useful in cases
where you are not sure if the syntax you are using in the Logstash
file input filters is correct or not. This can happen for example for
the 'exclude' field of the file input filter, for which the
documentation in Logstash is not clear. The 'exclude' field is crucial
for example for the exclusion of the Beats log files, which must be
present on the FS for being signed, but must not be reindexed in
Elasticsearch.

To check if your Logstash file input filter is correctly not parsing a
file, you can create a file X which you expect to be parsed by
logstash and a file Y that should not be parsed by Logstash. Then, as
soon as you see that Logstash is reading the file X, check if file Y
is read.

This procedure is suggested because you have to consider that Logstash
takes a while to parse the files, but when it parses one file, it
parses all of the files. So if we see that file X is read and file Y
is not read, we are sure that Logstash is actually not parsing file Y.

So, you can do the following:

1. Create a file X which should be always parsed by logstash and a
   file Y that should not be parsed by Logstash, and the permissions
   of both files, and the of the paths to the files, so that the
   logstash system user can read them

2. Restart Logstash

3. Check which files the logstash process is reading with the 'lsof'
   command::

     lsof -p &lt logstash_pid &gt

4. If logstash is not reading file X, then repeat the lsof command,
   otherwise you already are seeing all the files logstash is parsing
