coffeesprout.rsyslog
=========

Installs rsyslog.
Sets Logrotate

Both are essentially the same on RHEL and Debian

Requirements
------------

No other roles, tested on Rhel based systems (7 and 8) and Debian 11

Role Variables
--------------

Here are some of the included defaults:

	ryslog_packages:
	- rsyslog
	- rsyslog-gnutls

Enables TLS and will include dependencies (gnu-tls)

	rsyslog\_ca\_url:

Optional URL to download the CA cert from for TLS support

	rsyslog\_ca\_file: "/etc/rsyslog-ca.pem"

Optional name and location where to store the downloaded CA cert

	rsyslog\_ca\_hash:

Optional checksum of type <algorithm>:<checksum>  

  rsyslogd\_files:

Used to generate files in /etc/rsyslog.d/
The name is applied to the filename and the list of options is a set of strings that will be written inside the file

Example
   - name: central\_logserver\_r\_us
     options:
     - "$ModLoad imudp.so"
     - "$UDPServerRun 514"
   - name: remote
     options:
     - "*.* @loghost.example.com"

  logrotate\_default\_options:

The default options in /etc/logrotate.conf; We stick with pretty much the default options in RHEL and Debian, but enable compression and cap file size at 100M (Which would generally yield 10M compressed files using gzip)

Dependencies
------------


Example Playbook
----------------

    - hosts: servers
      roles:
      - role: coffeesprout.rsyslog
        rsyslogd_files:
        - name: papertrail
          options:
          - "$ActionSendStreamDriver gtls"
          - "$ActionSendStreamDriverMode 1"
          - "$ActionSendStreamDriverAuthMode x509/name"
          - "$ActionSendStreamDriverPermittedPeer *.papertrailapp.com"
          - "$ActionResumeInterval 10"
          - "$ActionQueueSize 100000"
          - "$ActionQueueDiscardMark 97500"
          - "$ActionQueueHighWaterMark 80000"
          - "$ActionQueueType LinkedList"
          - "$ActionQueueFileName papertrailqueue"
          - "$ActionQueueCheckpointInterval 100"
          - "$ActionQueueMaxDiskSpace 2g"
          - "$ActionResumeRetryCount -1"
          - "$ActionQueueSaveOnShutdown on"
          - "$ActionQueueTimeoutEnqueue 10" 
          - "$ActionQueueDiscardSeverity 0"
          - "*.* @@logs.papertrailapp.com"
	      rsyslog_ca_url: "https://papertrailapp.com/tools/papertrail-bundle.pem"
	      rsyslog_ca_file: "/etc/papertrail-bundle.pem"
	      rsyslog_ca_hash: "md5:c75ce425e553e416bde4e412439e3d09"
	 

License
-------

BSD

Author Information
------------------

An optional section for the role authors to include contact information, or a website (HTML is not allowed).
