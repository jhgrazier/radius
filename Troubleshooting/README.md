## Troubleshooting Radius

### Issue with Expired Certificate

Experienced an issue authenticating client. The SSL handshake was not completing on packet #38 between client and server and as such an access-reject was being given.

After observing this condition in the packet capture, I used openssl to validate the ssl certificate expiration date on the Free Radius server and validated the certificate expired and then wrote an Ansible playbook to renew the certificate on the Free Radius nodes.

```bash
[root@freeradius-1 ~]# openssl x509 -enddate -noout -in /etc/raddb/certs/radius.domain.org.crt
notAfter=Aug 10 00:05:11 2024 GMT
```

I validated this condition in the logs of the Free Radius server, as well.

```bash
eap: Peer sent EAP Response (code 2) ID 43 length 17
(12) eap: Continuing tunnel setup
(12)     [eap] = ok
(12)   } # authorize = ok
(12) Found Auth-Type = eap
(12) # Executing group from file /etc/raddb/sites-enabled/default
(12)   authenticate {
(12) eap: Removing EAP session with state 0x31ec56c032c743c2
(12) eap: Previous EAP request found for state 0x31ec56c032c743c2, released from the list
(12) eap: Peer sent packet with method EAP TTLS (21)
(12) eap: Calling submodule eap_ttls to process data
(12) eap_ttls: Authenticate
(12) eap_ttls: (TLS) EAP Peer says that the final record size will be 7 bytes
(12) eap_ttls: (TLS) EAP Got all data (7 bytes)
(12) eap_ttls: (TLS) TTLS - recv TLS 1.2 Alert, warning close_notify
(12) eap_ttls: (TLS) TTLS - The client is informing us that the connection has been closed, and no further TLS exchanges will take place.
(12) eap_ttls: (TLS) TTLS - Server : Need to read more data: SSLv3/TLS write server done
(12) eap_ttls: (TLS) TTLS - In Handshake Phase
(12) eap_ttls: (TLS) TTLS - Application data.
(12) eap_ttls: ERROR: (TLS) Cannot continue, as the peer is misbehaving.
(12) eap_ttls: ERROR: [eaptls process] = fail
(12) eap: ERROR: Failed continuing EAP TTLS (21) session.  EAP sub-module failed
(12) eap: Sending EAP Failure (code 4) ID 43 length 4
(12) eap: Failed in EAP select
(12)     [eap] = invalid
(12)   } # authenticate = invalid
(12) Failed to authenticate the user
(12) Using Post-Auth-Type Reject
(12) # Executing group from file /etc/raddb/sites-enabled/default
(12)   Post-Auth-Type REJECT {
(12) sql: EXPAND .query
(12) sql:    --> .query
(12) sql: Using query template 'query'
rlm_sql (sql): Reserved connection (2)

### Link to the Playbook to Resolve the Issue
[Free Radius Playbooks](https://github.com/jhgrazier/ansible-playbooks/tree/main/freeradius/README.md)
