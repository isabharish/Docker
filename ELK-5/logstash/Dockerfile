FROM logstash:5

# add custom config
COPY logstash.conf /etc/logstash/conf.d/logstash.conf
COPY ./Certs/ /etc/logstash/conf.d/certs/

# add templates
COPY ./templates/ /etc/logstash/conf.d/templates/
