Skip to content
 
Search…
All gists
Back to GitHub
Sign in
Sign up
Instantly share code, notes, and snippets.

@gorakhargosh gorakhargosh/pdnsd.conf
Created 8 years ago
61
 Code Revisions 1 Stars 6 Forks 1
<script src="https://gist.github.com/gorakhargosh/1119508.js"></script>
  
pdnsd.conf
 pdnsd.conf
// Sample pdnsd configuration file. Must be customized to obtain a working pdnsd setup!
// Read the pdnsd.conf(5) manpage for an explanation of the options.
// Add or remove '#' in front of options you want to disable or enable, respectively.
// Remove '/*' and '*/' to enable complete sections.

global {
	perm_cache=999999;
	cache_dir="/usr/local/var/cache/pdnsd";
#	pid_file = /var/run/pdnsd.pid;
	run_as="nobody";
	server_ip = 127.0.0.1;  # Use eth0 here if you want to allow other
				# machines on your network to query pdnsd.
	status_ctl = on;
#	paranoid=on;       # This option reduces the chance of cache poisoning
	                   # but may make pdnsd less efficient, unfortunately.
	query_method=udp_tcp;
	min_ttl=1d;       # Retain cached entries at least 1 day.
	max_ttl=2d;        # Two days
	timeout=10;        # Global timeout option (10 seconds).
	neg_domain_pol=on;
}

# The following section is most appropriate if you have a fixed connection to
# the Internet and an ISP which provides good DNS servers.
server {
	label= "Google Public DNS";
	ip = 8.8.8.8, 8.8.4.4, 4.2.2.2;  # Put your ISP's DNS-server address(es) here.
	proxy_only=on;     # Do not query any name servers beside your ISP's.
	                   # This may be necessary if you are behind some
	                   # kind of firewall and cannot receive replies
	                   # from outside name servers.
	timeout=4;         # Server timeout; this may be much shorter
			   # that the global timeout option.
	uptest=ping;         # Test if the network interface is active.
	ping_timeout=100;
    #interface=eth0;    # The name of the interface to check.
	interval=10m;      # Check every 10 minutes.
	purge_cache=off;   # Keep stale cache entries in case the ISP's
			           # DNS servers go offline.
}

/*
# The following section is more appropriate for dial-up connections.
# Read about how to use pdnsd-ctl for dynamic configuration in the documentation.
server {
	label= "dialup";
	file = "/etc/ppp/resolv.conf";  # Preferably do not use /etc/resolv.conf
	proxy_only=on;
	timeout=4;
	uptest=if;
	interface = ppp0;
	interval=10;       # Check the interface every 10 seconds.
	purge_cache=off;
	preset=off;
}
*/

/*
# The servers provided by OpenDNS are fast, but they do not reply with
# NXDOMAIN for non-existant domains, instead they supply you with an
# address of one of their search engines. They also lie about the addresses of 
# of the search engines of google, microsoft and yahoo.
# If you do not like this behaviour the "reject" option may be useful.
server {
	label = "opendns";
	ip = 208.67.222.222, 208.67.220.220;
	reject = 208.69.32.0/24,  # You may need to add additional address ranges
	         208.69.34.0/24,  # here if the addresses of their search engines
	         208.67.219.0/24; # change.
	reject_policy = fail;     # If you do not provide any alternative server
	                          # sections, like the following root-server
	                          # example, "negate" may be more appropriate here.
	timeout = 4;
	uptest = ping;            # Test availability using ICMP echo requests.
        ping_timeout = 100;       # ping test will time out after 10 seconds.
	interval = 15m;           # Test every 15 minutes.
	preset = off;
}
*/

/*
# This section is meant for resolving from root servers.
server {
	label = "root-servers";
	root_server = discover; # Query the name servers listed below
				# to obtain a full list of root servers.
	randomize_servers = on; # Give every root server an equal chance
	                        # of being queried.
	ip = 	198.41.0.4,     # This list will be expanded to the full
		192.228.79.201; # list on start up.
	timeout = 5;
	uptest = query;         # Test availability using empty DNS queries.
	interval = 30m;         # Test every half hour.
	ping_timeout = 300;     # Test should time out after 30 seconds.
	purge_cache = off;
	exclude = .localdomain;
	policy = included;
	preset = off;
}
*/

source {
	owner=localhost;
#	serve_aliases=on;
	file="/etc/hosts";
}

/*
include {file="/etc/pdnsd.include";}	# Read additional definitions from /etc/pdnsd.include.
*/

rr {
	name=localhost;
	reverse=on;
	a=127.0.0.1;
	owner=localhost;
	soa=localhost,root.localhost,42,86400,900,86400,86400;
}

/*
neg {
	name=doubleclick.net;
	types=domain;   # This will also block xxx.doubleclick.net, etc.
}
*/

/*
neg {
	name=bad.server.com;   # Badly behaved server you don't want to connect to.
	types=A,AAAA;
}
*/
 to join this conversation on GitHub. Already have an account? Sign in to comment
© 2019 GitHub, Inc.
Terms
Privacy
Security
Status
Help
Contact GitHub
Pricing
API
Training
Blog
About
