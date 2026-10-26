logger -p user.notice -t RSYSLOGTEST "teszt uzenet lpar3 -> lpar2 -> lpar1 $(date)"



LPAR1: 192.168.0.1

LPAR2: 192.168.0.2 .... 192.168.64.2

LPAR3: 192.168.64.3



LPAR3-ról LPAR2-re (port 6517):
timeout 3 bash -c "</dev/tcp/192.168.64.2/6517" && echo "Connection OK"

LPAR2-ről LPAR1-re (port 6517):
timeout 3 bash -c "</dev/tcp/192.168.0.1/6517" && echo "Connection OK"




LPAR1-en (reveiver):

firewall-cmd --permanent --add-port=6514/tcp

firewall-cmd --permanent --add-port=6517/tcp

firewall-cmd --reload

LPAR2-n (sender receiver:

firewall-cmd --permanent --add-port=6514/tcp

firewall-cmd --permanent --add-port=6517/tcp

firewall-cmd --reload





journalctl -u rsyslog -n 50 --no-pager | grep -iE 'relp|tls|gnutls|error|fail'



openssl s_client -connect 192.168.64.2:6517 \
    -CAfile /logging/lpar2-ca.crt \
    -cert /logging/lpar2-client.crt \
    -key /logging/lpar2-client-key.pem
