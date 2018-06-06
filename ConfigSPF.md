### Set up SPF (Sender Policy Framework) on ubuntu
# Add SPF TXT record to DNS (outgoing check)
1. add the following record to your DNS
@       IN      TXT     "v=spf1 +a +ip4:188.166.253.49 +mx -all"
2. restart bind9

``` 213 ```

# (incoming check)
1. install the postfix-policyd-spf-python package:
