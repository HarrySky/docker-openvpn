FROM alpine:3.21.0
LABEL maintainer="Igor Nehoroshev <hi@neigor.me>"

WORKDIR /etc/openvpn
RUN apk add --no-cache openvpn \
    # Create folder for client specific overrides and keys/certs
    && mkdir -p /etc/openvpn/ccd /etc/openvpn/pki \
    # Create info/state files
    && touch /etc/openvpn/ipp.txt /var/log/openvpn_status.log \
    # Forward logs to docker log collector
    && ln -sf /dev/stdout /var/log/openvpn.log

CMD [ "/usr/sbin/openvpn", "--suppress-timestamps", "--config", "/etc/openvpn/server.conf" ]
