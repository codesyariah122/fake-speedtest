# fake-speedtest

# fake speedtest squid-3.x.x
puji ermanto aka mamam yuk<br>
disini saya menggunakan OS debian7<br>
langsung clone ajah dimari
* clone lewat github :
```bash
git clone https://github.com/puji122/fake-speedtest.git
cp speedtest/ /var/www/
chown -R www-data:www-data /var/www/
chmod 755 /var/www/
# direktori folder root webserver debian7
* /var/www/speedtest
# ==============================================================================================================
atau kalian bisa dengan metode drag & drop dengan winscp untuk mempersingkat waktu
```
# tambahkan config di bawah pada squid.conf
```bash
# =======================================================================
acl speedtest url_regex -i \/speedtest\/.*\.(jpg|png|txt|php).*
acl speedtest2 url_regex -i ^http:\/\/.*speedtest\.net\/id\/.*\.php.*
acl speedtest3 url_regex -i ^http:\/\/.*api\.ookla\.com\/.*\.php
url_rewrite_access allow speedtest
url_rewrite_access allow speedtest2
url_rewrite_access allow speedtest3 
url_rewrite_access deny all
url_rewrite_program /etc/squid/rewritespeedtest.pl
redirector_bypass on
cache_peer 192.168.9.200 parent 80 0 no-digest no-tproxy #replace with your ipproxy
dead_peer_timeout 5 seconds
cache_peer_access 192.168.9.200 allow speedtest #replace with your ipproxy
cache_peer_access 192.168.9.200 allow speedtest2 #replace with your ipproxy
cache_peer_access 192.168.9.200 allow speedtest3 #replace with your ipproxy
cache_peer_access 192.168.9.200 deny all
always_direct deny speedtest
never_direct allow speedtest
always_direct deny speedtest2
never_direct allow speedtest2
always_direct deny speedtest3
never_direct allow speedtest3
url_rewrite_children 32 startup=10 idle=1 concurrency=50
# =======================================================================
```
# buat rewriter speedtest nya 
* cd /etc/squid/
* buat rewritespeedtest.pl
* setting permission nya
```bash
cd /etc/squid/
touch rewritespeedtest.pl
chown -R proxy:proxy /etc/squid/rewritespeedtest.pl
chmod +x /etc/squid/rewritespeedtest.pl
```
```bash
#!/usr/bin/perl
# @ http://www2.fh-lausitz.de/…/comp/misc/squid/projekt_youtube/
# referensi dan terimakasih khususnya pada MikroTiker N SquidLover-Ces Pun-Syaifudin JW aka Ucok Karnadi
# di publish oleh mamam yuk 
#### var
use IO::File;
$|=1;
STDOUT->autoflush(1);
$debug=0; ## recommended:0
$bypassallrules=0; ## recommended:0
$sucks=""; ## unused
$sucks="sucks" if ($debug>=1);
$timenow="";
$printtimenow=1; ## print timenow: 0|1
my $logfile = '/tmp/rewrite-ruwet.log';

open my $logfh, '>>', $logfile
or die "Couldn't open $logfile for appending: $!\n" if $debug;
$logfh->autoflush(1) if $debug;

while (<>) {
$timenow=time()." " if ($printtimenow);
#print $logfh "$timenow"."in : $_" if ($debug>=1);
#print $logfh "in : $_" if ($debug>=1);
chop; ## strip eol
my $urlku = $_;
#@X = split;
@X = split(" ",$urlku);
$a = $X[0]; ## chanel
$b = $X[1]; ## url
$c = $X[2]; ## ip
$u = $b; ## url

print $logfh "in : $_\n" if ($debug>=1);

if ($bypassallrules){
$out="$u"; ## map 1:1

} elsif ($u=~ m/^http:\/\/.*\/(speedtest\/.*)/) {
$out="OK rewrite-url=http://192.168.9.200:80/$1";

} elsif ($u=~ m/^http:\/\/.*speedtest\.net\/id\/(.*)/) {
$out="OK rewrite-url=http://192.168.9.200:80/speedtest/$1";

} elsif ($u=~ m/^http:\/\/.*api\.ookla\.com\/(.*)/) {
$out="OK rewrite-url=http://192.168.9.200:80/speedtest/$1";

} else {
$out="ERR";
}
print $logfh "out: $a $out\n" if ($debug>=1);
print "$a $out\n";
}
close $logfh if ($debug);
```

# keterangan
* 192.168.9.200 = ip address proxy server / webserver /ubuntu server /debian server
* speedtest = cache_peer untuk fake speedtest<br>
# ================The End================= 

