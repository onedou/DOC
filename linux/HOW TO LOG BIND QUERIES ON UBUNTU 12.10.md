https://www.gypthecat.com/how-to-log-bind-queries-on-ubuntu-12-10

I’ve been troubleshooting some pretty large networks lately, and since DNS underpins most enterprise networks it’s very useful to see what traffic is going through the DNS servers.  By default Ubuntu doesn’t log every query, and I can understand why.  The average home network generates 100’s of DNS queries an hour, enterprise networks generate magnitudes of scale more.


Option 1 – Quick and Dirty
You can quickly turn on logging by typing in the following into the server shell:
rndc querylog
Then you can follow the information in the standard syslog.
tail -f /var/log/syslog
You should see output like the following letting you know that queries are now logged:

Sep 14 22:23:20 ns01.companya.local named[7896]: query logging is now on

Option 2 – Full and Stored Logs
If you want to store full logs that you can go back to at a later date you’ll need to make some changes to the BIND configuration.
Logon to your shell as usual, and type the following:
nano /etc/bind/named.conf
Put in the following code at the bottom:
logging {
channel query.log {
file "/var/log/query.log";
severity debug 3;
};
category queries { query.log; };
};
Now we need to create the log:
touch /var/log/query.log
Make it writable by the BIND process:
chown named.named /var/log/query.log
Give BIND a reboot:
service bind9 restart
And now you should be able to follow the queries as any other log:
tail -f /var/log/query.log
