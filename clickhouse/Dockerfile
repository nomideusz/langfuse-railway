# Langfuse's trace store. Stock image (it already listens on :: for Railway's
# IPv6 private network) plus one config file that keeps it small: logs go to
# the console instead of 10 x 1 GB files on the container disk, and the
# system tables that record ClickHouse's own metrics every second are off.
FROM clickhouse/clickhouse-server:25.12.11.4

COPY railway.xml /etc/clickhouse-server/config.d/railway.xml
# The stock file listens on :: and 0.0.0.0; on Railway :: is already
# dual-stack, so the second bind fails with an "Address already in use"
# warning on every boot. Overwritten, not deleted: Railway resurrects files
# removed in a later layer.
COPY listen.xml /etc/clickhouse-server/config.d/docker_related_config.xml

ENV CLICKHOUSE_DB=default \
    CLICKHOUSE_USER=langfuse
