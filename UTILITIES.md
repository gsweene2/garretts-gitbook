# Utilities

<!-- toc -->

## Find a domains's DNS servers

### `host`

```
$ host -t ns somedomain.com
somedomain.com name server dns3.p05.nsone.net.
somedomain.com name server dns1.p05.nsone.net.
somedomain.com name server dns4.p05.nsone.net.
somedomain.com name server dns2.p05.nsone.net.
```

### `dig`
```
$ dig ns somedomain.com.com

; <<>> DiG 9.10.6 <<>> ns somedomain.com.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 51928
;; flags: qr rd ra; QUERY: 1, ANSWER: 4, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
;; QUESTION SECTION:
;somedomain.com.com.		IN	NS

;; ANSWER SECTION:
somedomain.com.com.	4264	IN	NS	dns3.p05.nsone.net.
somedomain.com.com.	4264	IN	NS	dns1.p05.nsone.net.
somedomain.com.com.	4264	IN	NS	dns4.p05.nsone.net.
somedomain.com.com.	4264	IN	NS	dns2.p05.nsone.net.

;; Query time: 11 msec
;; SERVER: 172.20.10.1#53(172.20.10.1)
;; WHEN: Sun Jul 11 13:02:33 CDT 2021
;; MSG SIZE  rcvd: 134
```
