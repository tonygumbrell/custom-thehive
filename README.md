# custom-thehive

# Elasticsearch
search {
  # Name of the index
  index = the_hive
  # Name of the Elasticsearch cluster
  cluster = hive
  # Address of the Elasticsearch instance
  host = ["127.0.0.1:9300"]
  # Scroll keepalive
  keepalive = 1m
  # Size of the page for scroll
  pagesize = 50
  # Number of shards
  nbshards = 5
  # Number of replicas
  nbreplicas = 1
  # Arbitrary settings
  settings {
    # Maximum number of nested fields
    mapping.nested_fields.limit = 100
  }

  ### XPack SSL configuration
  # Username for XPack authentication
  #search.username
  # Password for XPack authentication
  #search.password
  # Enable SSL to connect to ElasticSearch
  search.ssl.enabled = false
  # Path to certificate authority file
  #search.ssl.ca
  # Path to certificate file
  #search.ssl.certificate
  # Path to key file
  #search.ssl.key

  ### SearchGuard configuration
  # Path to JKS file containing client certificate
  #search.guard.keyStore.path
  # Password of the keystore
  #search.guard.keyStore.password
  # Path to JKS file containing certificate authorities
  #search.guard.trustStore.path
  ## Password of the truststore
  #search.guard.trustStore.password
  # Enforce hostname verification
  #search.guard.hostVerification
  # If hostname verification is enabled specify if hostname should be resolved
  #search.guard.hostVerificationResolveHostname
}

# Datastore
datastore {
  name = data
  # Size of stored data chunks
  chunksize = 50k
  hash {
    # Main hash algorithm /!\ Don't change this value
    main = "SHA-256"
    # Additional hash algorithms (used in attachments)
    extra = ["SHA-1", "MD5"]
  }
  attachment.password = "malware"
}

auth {
	# "provider" parameter contains authentication provider. It can be multi-valued (useful for migration)
	# available auth types are:
	# services.LocalAuthSrv : passwords are stored in user entity (in Elasticsearch). No configuration is required.
	# ad : use ActiveDirectory to authenticate users. Configuration is under "auth.ad" key
	# ldap : use LDAP to authenticate users. Configuration is under "auth.ldap" key
	provider = [local]

  # By default, basic authentication is disabled. You can enable it by setting "method.basic" to true.
  method.basic = false

	ad {
		# The name of the Microsoft Windows domain using the DNS format. This parameter is required.
		#domainFQDN = "mydomain.local"

    # Optionally you can specify the host names of the domain controllers. If not set, TheHive uses "domainFQDN".
    #serverNames = [ad1.mydomain.local, ad2.mydomain.local]

		# The Microsoft Windows domain name using the short format. This parameter is required.
		#domainName = "MYDOMAIN"

		# Use SSL to connect to the domain controller(s).
		#useSSL = true
	}

	ldap {
		# LDAP server name or address. Port can be specified (host:port). This parameter is required.
		#serverName = "ldap.mydomain.local:389"

    # If you have multiple ldap servers, use the multi-valued settings.
    #serverNames = [ldap1.mydomain.local, ldap2.mydomain.local]

		# Use SSL to connect to directory server
		#useSSL = true

		# Account to use to bind on LDAP server. This parameter is required.
		#bindDN = "cn=thehive,ou=services,dc=mydomain,dc=local"

		# Password of the binding account. This parameter is required.
		#bindPW = "***secret*password***"

		# Base DN to search users. This parameter is required.
		#baseDN = "ou=users,dc=mydomain,dc=local"

		# Filter to search user {0} is replaced by user name. This parameter is required.
		#filter = "(cn={0})"
	}
}

# Maximum time between two requests without requesting authentication
session {
  warning = 5m
  inactivity = 1h
}

# Streaming
stream.longpolling {
  # Maximum time a stream request waits for new element
  refresh = 1m
  # Lifetime of the stream session without request
  cache = 15m
  nextItemMaxWait = 500ms
  globalMaxWait = 1s
}

# Max textual content length
play.http.parser.maxMemoryBuffer=1M
# Max file size
play.http.parser.maxDiskBuffer=1G

## Enable the Cortex module
play.modules.enabled += connectors.cortex.CortexConnector

cortex {
  "CORTEX-SERVER-ID" {
    # URL of the Cortex server
    url = "http://CORTEX_SERVER:CORTEX_PORT"
    # Key of the Cortex user, mandatory for Cortex 2
    key = "API key"
  }
  # HTTP client configuration, more details in section 8
  # ws {
  #   proxy {}
  #   ssl {}
  # }
  # Check job update time interval
  refreshDelay = 1 minute
  # Maximum number of successive errors before give up
  maxRetryOnError = 3
  # Check remote Cortex status time interval
  statusCheckInterval = 1 minute
}

## Enable the MISP module (import and export)
play.modules.enabled += connectors.misp.MispConnector

misp {
  "MISP-SERVER-ID" {
    # URL of the MISP instance.
    url = "https://1.2.3.4"

    # Authentication key.
       key = "abcd"

    # Name of the case template in TheHive that shall be used to import
    # MISP events as cases by default.
    caseTemplate = "TEST"

    # Tags to add to each observable imported from an event available on
    # this instance.
    tags = ["misp"]

    # Truststore to use to validate the X.509 certificate  of  the  MISP
    # instance if the default truststore is not sufficient.
   #  ws.ssl.trustManager.stores = [
   #  {
    #  type: "JKS"
    #  path: "/opt/thehive/conf/KeyStore.jks"
    #  }
  #  ]

# HTTP client configuration, more details in section 8
    # ws {
    #   proxy {}
    #   ssl {}
    # }

    # filters:
    max-attributes = 1000
    max-size = 1 MiB
    max-age = 7 days
    exclusion {
     organisation = ["bad organisation", "other orga"]
     tags = ["tag1", "tag2"]
    }

    # MISP purpose defines if this instance can be used to import events (ImportOnly), export cases (ExportOnly) or both (ImportAndExport)
    # Default is ImportAndExport
    purpose = ImportAndExport
  }

  # Interval between consecutive MISP event  imports  in  hours  (h)  or
  # minutes (m).
  interval = 1h
}

    https.port: 9443
    play.server.https.keyStore {
      path: "/path/to/keystore.jks"
      type: "JKS"
      password: "password_of_keystore"
    }
