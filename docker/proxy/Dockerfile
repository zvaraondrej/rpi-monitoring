FROM nginx:stable-alpine

ENV PROXY_UPSTREAM_NODEEXPORTER_HOST=node-exporter
ENV PROXY_UPSTREAM_NODEEXPORTER_PORT=9100
ENV PROXY_UPSTREAM_CADVISOR_HOST=cadvisor
ENV PROXY_UPSTREAM_CADVISOR_PORT=8080
ENV PROXY_UPSTREAM_PROMETHEUS_HOST=prometheus
ENV PROXY_UPSTREAM_PROMETHEUS_PORT=9090
ENV PROXY_UPSTREAM_GRAFANA_HOST=grafana
ENV PROXY_UPSTREAM_GRAFANA_PORT=3000

COPY docker/proxy/nginx.conf.tpl /etc/nginx/nginx.conf.tpl
COPY docker/proxy/nginx.crt /etc/nginx/nginx.crt
COPY docker/proxy/nginx.key /etc/nginx/nginx.key

COPY docker/proxy/main.sh /main.sh
RUN chmod +x /main.sh

EXPOSE 80
EXPOSE 443

ENTRYPOINT ["sh","/main.sh"]

