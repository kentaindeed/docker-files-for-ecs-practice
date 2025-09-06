FROM mysql:8.2

# Copy custom MySQL configuration
COPY ./docker/mysql/my.cnf /etc/mysql/my.cnf

# Create log directory for slow query log
RUN mkdir -p /var/log/mysql && \
    chown mysql:mysql /var/log/mysql
