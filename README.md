<h1> DKIM (Domain Keys Identified Mail) config with postfix
<img src="https://www.dmarcanalyzer.com/app/uploads/2017/04/what-is-DKIM-domainkeysidentifiedmail-1.png" align=right width=20% />
</h1>
<br>


***About DKIM***

DKIM provides a way for senders to confirm their identity when sending email by adding a cryptographic signature to the headers of the message.


***How To***


##### 1. Make sure your system is updated
> sudo apt-get update
> 
> sudo apt-get upgrade

##### 2. Install OpenDKIM
> sudo apt-get -y install opendkim

##### 3. Install OpenDKIM Tools
> sudo apt-get -y install opendkim-tools

##### 4. Open and edit _/etc/opdendkim.conf_ file to add or uncomment the following lines:
> Domain  mydomain.com
> KeyFile /etc/posftix/dkim.key
> 
> Selector  mail _(can be whatever you want, make it easy to remain it)_
> 
> Socket  inet:8891@localhost

Save your changes and exit the prompt.

##### 5. Open and edit _/etc/default/opendkim_ file to change the default socket
>SOCKET="inet:8891@localhost"

##### 6. Open _/etcpostfix/main.cf_ file to add
> #DKIM Config
> 
> milter_default_action = accept
> 
> milter_protocol = 2
> 
> smtpd_milters = inet:localhost:8891
> 
> non_smtpd_milters = inet:localhost:8891

##### 7. Let´s create the DKIM Keys, so run the following command. Change [..] with your information.
> opendkim-genkey -t -s [your selector from step #4] -d [mydomain]

After running it you will see two files, one with the private key selector.private and selector.txt. The first one is used to sign to outgoing emails so it should be placed in postfix directory.

##### 8. Run the following command to move the private dkim key to postfix directory especified in the _KeyFile_ of the Step #4.
> cp [yourselector].private /etc/postfix/dkim.key

##### 9. It´s time to set up the DNS. Check the [selector].txt created in Step #8 to extract the data we need. Run:
> cat [yourselector].txt

Then you will see something like:

> _mail._domainkey IN      TXT     ( "v=DKIM1; h=sha256; k=rsa; t=y; "
          "p=MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAqpl1fOh3CwHCYdnoTkC9Qf3n2u5CBihoUqbmx0+fxi/W6RvmwNBw+vTWnxqZIX9z4if7LSxWE849Qp8aztFuZXTWB/6i5f77GIRI7qefysFpnhmGIBbUnh1amZ7lc2wtYG2gfDZLuIGKOx4RzKw2YmlbXna1dbICy58sY5EtDhnxvXqtGilzAEa3tN4UeKJfkSbl60a792Sgu4"
 )_
          
Open your dns panel to add a TXT where the HOST will be your domain key [the text before the IN TXT]. The VALUE will be the long string starting with v=DKIM1...
Save your changes and wait for the propagation.

##### 9 Go back to your server to start DKIM service and restart postfix.
> service opendkim start
> 
> service postfix restart








