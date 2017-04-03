# mage.nginx-proxy

Nginx acting as a ssl terminator and proxy server routing requests to containerized web servers. Works best with mage.letsencrypt

## Example playbook

```
- hosts: proxy
  vars:
      nginx_proxy_default_certificate:         "/etc/letsencrypt/live/example.com/fullchain.pem"
      nginx_proxy_default_certificate_key:     "/etc/letsencrypt/live/example.com/privkey.pem"
      nginx_proxy_default_trusted_certificate: "/etc/letsencrypt/live/example.com/fullchain.pem"
      letsencrypt:
         - { email: "root@example.com", domains: ["example.com", "loo.com", "foo.com" ] }
      nginx_proxy_upstream:
      - name: "foo_com"
        servers: [ "foo_com:80" ]
      - name: "loo_com"
        servers: [ "10.0.0.50:80" ]
      - name: "example_com"
        servers: [ "example_com:80" ]
      nginx_proxy_server:
      - name: "foo.com"
        locations:
          - { name: "/", proxy_pass: "http://foo_com/" }
      - name: "loo.com"
        locations:
          - { name: "/", proxy_pass: "http://loo_com/" }
      - name: "example.com"
        extra_parameters: |
          # this shows a redirect test
          return 301 https://example.net;
  roles:
    - role: mage.nginx-proxy
    - role: mage.letsencrypt
    - role: mage.nginx-proxy
    # yes, running a role twice is stupid, but it mages the magic work

