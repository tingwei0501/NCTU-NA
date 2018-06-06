## Set up SPF (Sender Policy Framework) on ubuntu
### Add SPF TXT record to DNS (outgoing check)
1. add the following record to your DNS
`&#8212;`ruby
@       IN      TXT     "v=spf1 +a +ip4:YOUR_IP +mx -all"
`&mdash;`
2. restart bind9
```ruby
/etc/init.d/bind9 restart
```
or 
```ruby
service bind9 restart
```
You can check the status of your service
```ruby
/etc/init.d/bind9 status
```

### Add SPF policy to postfix (incoming check)
You will need to use SpamAssassin to filter spam.
1. install the postfix-policyd-spf-python package:
```ruby
apt-get install postfix-policyd-spf-python
```
2. edit policyd-spf.conf
```ruby
vim /etc/postfix-policyd-spf-python/policyd-spf.conf
```
```ruby
HELO_reject = False
Mail_From_reject = False
```
3. edit master.cf
```ruby
vim /etc/postfix/master.cf
```
add the following entry at the end
```ruby
policyd-spf  unix  -       n       n       -       0       spawn
    user=policyd-spf argv=/usr/bin/policyd-spf
```
4. edit main.cf
```ruby
vim /etc/postfix/main.cf
```
edit *smtpd_recipient_restrictions entry* to add a *check_policy_service* entry.
Make sure to add *check_policy_service* **after** reject_unauth_destination
```ruby
smtpd_recipient_restrictions =
  ...
  reject_unauth_destination,
  check_policy_service unix:private/policyd-spf,
  ...
```
5. add the following entry at the end
```ruby
policyd-spf_time_limit = 3600
```
6. restart postfix
```ruby
systemctl restart postfix
```
or 
```ruby
/etc/init.d/postfix restart
```

### Check your SPF servive
You can check your SPF by looking at raw headers on incoming mails, if you see the following form, congrets!!
```ruby
Received-SPF: Pass
```
