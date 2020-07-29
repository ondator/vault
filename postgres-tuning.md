# Postgres tuning

Here is settings list I am always modify for new Postgres installation.

### postgresql.conf

+ shared_buffers = 1/3 of your RAM
+ huge_pages = on
+ maintenance_work_mem = autovacuum_work_mem = 1Gb
+ dynamic_shared_memory_type = posix
+ wal_buffers = 64Mb
+ wal_writer_delay = 200ms
+ wal_compression = on
+ wal_log_hints = on
+ max_wal_size = 1GB
+ min_wal_size = 80MB
+ vacuum_cost_page_dirty = 10
+ bgwriter_delay = 10ms		
+ bgwriter_lru_maxpages = 1000
+ bgwriter_lru_multiplier = 2.0	
+ bgwriter_flush_after = 512Kb
+ tcp_keepalives_idle = 5
+ tcp_keepalives_interval = 10
+ tcp_keepalives_count = 5
+ ssl = off
+ max_worker_processes = # of cores
+ checkpoint_timeout = 1h
+ seq_page_cost = 0.1 -- if ssd
+ random_page_cost = 0.1 -- if ssd
+ autovacuum_vacuum_cost_delay -- if ssd
+ log_autovacuum_min_duration = 1min
+ autovacuum_max_workers = 10
+ autovacuum_naptime = 1s
+ autovacuum_vacuum_scale_factor = 0.1
+ autovacuum_analyze_scale_factor = 0.05
+ autovacuum_vacuum_cost_delay = 20ms


### pgbouncer.ini

+ pool_mode = transaction