# postfix-macos-custom-mail
A repo documenting modifications to postfix on macos 
to enable `mail` cli to work properly.

# Getting started:
1. edit `/private/etc/postfix` to add these:
```
relayhost = smtp.mail.me.com:587
smtp_sasl_auth_enable = yes
smtp_sasl_password_maps = hash:/etc/postfix/sasl_passwd
smtp_sasl_security_options = noanonymous
smtp_sasl_mechanism_filter = login
smtp_tls_security_level = encrypt
smtp_tls_CAfile = /etc/ssl/cert.pem
smtp_tls_wrappermode = no
smtp_generic_maps = hash:/etc/postfix/generic
```
(This is identical to `main.cf` in the repo)

2. Go to apple account website, and generate a app-specific password

3. Edit `/private/etc/postfix/sasl_passwd` to add your credentials:
```
smtp.mail.me.com:587 somemail@icloud.com:app_specific_password
```
Do note you must use your pure-icloud account, custom domains 
won't work even if you set them as primary email for apple account.

4. Find your hostname using `hostname`

5. edit `/private/etc/postfix/generic` to add your email:
```
@yourhostname yourdesiredsendemail@somedomain
username@yourhostname yourdesiredsendemail@somedomain
```
Do note for your desired send email, you must configure it using 
iCloud before you can send email using it. Otherwise, pure-icloud 
email will work just fine.

6. In System Settings, go to `Privacy & Security` -> `Full Disk Access`, 
add an entry for `/usr/libexec/postfix/postfix-script`.
You can use `Cmd+Shift+.` to show hidden files in Finder.

7. Run these commands:
```
sudo postmap /etc/postfix/sasl_passwd
sudo postmap /etc/postfix/generic
sudo postfix start
sudo postfix reload
```

8. Install `fetchmail`:
```bash
brew install fetchmail
```

9. Edit `~/.fetchmailrc` to add these:
```
set daemon 300  # Fetch mail every 5 minutes
set logfile .fetchmail.log
poll imap.mail.me.com with proto IMAP
    user "somemail@icloud.com" there with password "aaaa-bbbb-cccc-dddd" is nick here
    ssl
    keep
	mda "/usr/sbin/sendmail -i %T"
```
