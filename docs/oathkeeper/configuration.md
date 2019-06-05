---
id: configuration
title: Configuration
---

```yaml
# ORY Oathkeeper Configuration
#
#
# !!WARNING!!
# This configuration file is for documentation purposes only. Do not use it in production. As all configuration items
# are enabled, it will not work out of the box either.
#
#
# ORY Oathkeeper can be configured using a configuration file and passing the file location using `-c path/to/config.yaml`.
# Per default, ORY Oathkeeper will look up and load file ~/.oathkeeper.yaml. All configuration keys can be set using environment
# variables as well.
#
# Setting environment variables is easy:
#
## Linux / OSX
#
# $ export MY_ENV_VAR=foo
# $ oathkeeper ...
#
# alternatively:
#
# $ MY_ENV_VAR=foo oathkeeper ...
#
## Windows
#
### Command Prompt
#
# > set MY_ENV_VAR=foo
# > oathkeeper ...
#
### Powershell
#
# > $env:MY_ENV_VAR="foo"
# > oathkeeper ...
#
## Docker
#
# $ docker run -e MY_ENV_VAR=foo oryd/oathkeeper:...
#
#
# Assuming the following configuration layout:
#
# serve:
#   public:
#     port: 4444
#     something_else: foobar
#
# Key `something_else` can be set as an environment variable by uppercasing it's path:
#   `serve.public.port.somethihng_else` -> `SERVE.PUBLIC.PORT.SOMETHING_ELSE`
# and replacing `.` with `_`:
#   `serve.public.port.somethihng_else` -> `SERVE_PUBLIC_PORT_SOMETHING_ELSE`
#
# Environment variables always override values from the configuration file. Here are some more examples:
#
# Configuration key | Environment variable |
# ------------------|----------------------|
# dsn               | DSN                  |
# serve.admin.host  | SERVE_ADMIN_HOST     |
# ------------------|----------------------|
#
#
# List items such as
#
# secrets:
#   system:
#     - this-is-the-primary-secret
#     - this-is-an-old-secret
#     - this-is-another-old-secret
#
# must be separated using `,` when using environment variables. The environment variable equivalent to the code section#
# above is:
#
# Linux/macOS: $ export SECRETS_SYSTEM=this-is-the-primary-secret,this-is-an-old-secret,this-is-another-old-secret
# Windows: > set SECRETS_SYSTEM=this-is-the-primary-secret,this-is-an-old-secret,this-is-another-old-secret

# log configures the logger
log:
  # Sets the log level, supports "panic", "fatal", "error", "warn", "info" and "debug". Defaults to "info".
  level: info
  # Sets the log format. Leave it undefined for text based log format, or set to "json" for JSON formatting.
  format: json

# Enables profiling if set. Use "cpu" to enable cpu profiling and "mem" to enable memory profiling. For more details
# on profiling, head over to: https://blog.golang.org/profiling-go-programs
profiling: cpu
# profiling: mem

# serve controls the configuration for the http(s) daemon(s).
serve:
  # public controls the public daemon serving public API endpoints like /oauth2/auth, /oauth2/token, /.well-known/jwks.json
  proxy:
    # The port to listen on. Defaults to 4444
    port: 4455
    # The interface or unix socket ORY Oathkeeper should listen and handle public API requests on.
    # Leave empty to listen on all interfaces.
    host: localhost # leave this out or empty to listen on all devices which is the default

    # Controls timeout
    timeout:
      # The maximum duration for reading the entire request, including the body.
      # Valid time units are "ns", "us" (or "µs"), "ms", "s", "m", "h". Defaults to 5s.
      read: 5s
      # The maximum duration before timing out writes of the response.
      #	Increase this parameter to prevent unexpected closing a client connection if an upstream request is executing more than 10 seconds.
      #	Valid time units are "ns", "us" (or "µs"), "ms", "s", "m", "h". Defaults ot 10s.
      write: 10s
      # The maximum amount of time to wait for any action of a request session, reading data or writing the response.
      #	Valid time units are "ns", "us" (or "µs"), "ms", "s", "m", "h". Defaults ot 120s.
      idle: 120s

    # cors configures Cross Origin Resource Sharing for public endpoints.
    cors:
      # set enabled to true to enable CORS. Defaults to false.
      enabled: true
      # allowed_origins is a list of origins (comma separated values) a cross-domain request can be executed from.
      # If the special * value is present in the list, all origins will be allowed. An origin may contain a wildcard (*)
      # to replace 0 or more characters (i.e.: http://*.domain.com). Only one wildcard can be used per origin.
      #
      # If empty or undefined, this defaults to `*`, allowing CORS from every domain (if cors.enabled: true).
      allowed_origins:
        - https://example.com
        - https://*.example.com
      # allowed_methods is list of HTTP methods the user agent is allowed to use with cross-domain
      # requests. Defaults to the methods listed.
      allowed_methods:
        - POST
        - GET
        - PUT
        - PATCH
        - DELETE

      # A list of non simple headers the client is allowed to use with cross-domain requests. Defaults to the listed values.
      allowed_headers:
        - Authorization
        - Content-Type

      # Sets which headers (comma separated values) are safe to expose to the API of a CORS API specification. Defaults to the listed values.
      exposed_headers:
        - Content-Type

      # Sets whether the request can include user credentials like cookies, HTTP authentication
      # or client side SSL certificates. Defaults to true.
      allow_credentials: true

      # Sets how long (in seconds) the results of a preflight request can be cached. If set to 0, every request
      # is preceded by a preflight request. Defaults to 0.
      max_age: 10

      # If set to true, adds additional log output to debug server side CORS issues. Defaults to false.
      debug: true

    # tls configures HTTPS (HTTP over TLS). If configured, the server automatically supports HTTP/2.
    tls:
      # key configures the private key (pem encoded)
      key:
        # The key can either be loaded from a file:
        path: /path/to/key.pem
        # Or from a base64 encoded (without padding) string:
        base64: LS0tLS1CRUdJTiBFTkNSWVBURUQgUFJJVkFURSBLRVktLS0tLVxuTUlJRkRqQkFCZ2txaGtpRzl3MEJCUTB3...

      # cert configures the TLS certificate (PEM encoded)
      cert:
        # The cert can either be loaded from a file:
        path: /path/to/cert.pem
        # Or from a base64 encoded (without padding) string:
        base64: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tXG5NSUlEWlRDQ0FrMmdBd0lCQWdJRVY1eE90REFOQmdr...

  # api controls the api daemon serving REST API endpoints like /rules, /judge, ...
  api:
    # The port to listen on. Defaults to 4445
    port: 4456
    # The interface or unix socket ORY Oathkeeper should listen and handle administrative API requests on.
    # Leave empty to listen on all interfaces.
    host: localhost # leave this out or empty to listen on all devices which is the default

    # cors configures Cross Origin Resource Sharing for admin endpoints.
    cors:
      # set enabled to true to enable CORS. Defaults to false.
      enabled: true
      # allowed_origins is a list of origins (comma separated values) a cross-domain request can be executed from.
      # If the special * value is present in the list, all origins will be allowed. An origin may contain a wildcard (*)
      # to replace 0 or more characters (i.e.: http://*.domain.com). Only one wildcard can be used per origin.
      #
      # If empty or undefined, this defaults to `*`, allowing CORS from every domain (if cors.enabled: true).
      allowed_origins:
        - https://example.com
        - https://*.example.com
      # allowed_methods is list of HTTP methods the user agent is allowed to use with cross-domain
      # requests. Defaults to GET and POST.
      allowed_methods:
        - POST
        - GET
        - PUT
        - PATCH
        - DELETE

      # A list of non simple headers the client is allowed to use with cross-domain requests. Defaults to the listed values.
      allowed_headers:
        - Authorization
        - Content-Type

      # Sets which headers (comma separated values) are safe to expose to the API of a CORS API specification. Defaults to the listed values.
      exposed_headers:
        - Content-Type

      # Sets whether the request can include user credentials like cookies, HTTP authentication
      # or client side SSL certificates.
      allow_credentials: true

      # Sets how long (in seconds) the results of a preflight request can be cached. If set to 0, every request
      # is preceded by a preflight request. Defaults to 0.
      max_age: 10

      # If set to true, adds additional log output to debug server side CORS issues. Defaults to false.
      debug: true

    # tls configures HTTPS (HTTP over TLS). If configured, the server automatically supports HTTP/2.
    tls:
      # key configures the private key (pem encoded)
      key:
        # The key can either be loaded from a file:
        path: /path/to/key.pem
        # Or from a base64 encoded (without padding) string:
        base64: LS0tLS1CRUdJTiBFTkNSWVBURUQgUFJJVkFURSBLRVktLS0tLVxuTUlJRkRqQkFCZ2txaGtpRzl3MEJCUTB3...

      # cert configures the TLS certificate (PEM encoded)
      cert:
        # The cert can either be loaded from a file:
        path: /path/to/cert.pem
        # Or from a base64 encoded (without padding) string:
        base64: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tXG5NSUlEWlRDQ0FrMmdBd0lCQWdJRVY1eE90REFOQmdr...

# Configures Access Rules
access_rules:
  # Locations (list of URLs) where access rules should be fetched from on boot.
  # It is expected that the documents at those locations return a JSON Array containing ORY Oathkeeper Access Rules.
  repositories:
    # If the URL Scheme is `file://`, the access rules (an array of access rules is expected) will be
    # fetched from the local file system.
    - file://path/to/rules.json
    # If the URL Scheme is `inline://`, the access rules (an array of access rules is expected)
    # are expected to be a base64 encoded (with padding!) JSON string (base64_encode(`[{"id":"foo-rule","authenticators":[....]}]`)):
    - inline://W3siaWQiOiJmb28tcnVsZSIsImF1dGhlbnRpY2F0b3JzIjpbXX1d
    # If the URL Scheme is `http://` or `https://`, the access rules (an array of access rules is expected) will be
    # fetched from the provided HTTP(s) location.
    - https://path-to-my-rules/rules.json

# All authenticators can be configured under this configuration key
authenticators:
  # Configures the anonymous authenticator
  anonymous:
    # Set enabled to true if the authenticator should be enabled and false to disable the authenticator. Defaults to false.
    enabled: true

    # Sets the anonymous username. Defaults to "anonymous". Common names include "guest", "anon", "anonymous", "unknown".
    subject: anonymous

  # Configures the jwt authenticator
  jwt:
    # Set enabled to true if the authenticator should be enabled and false to disable the authenticator. Defaults to false.
    enabled: true

    # REQUIRED IF ENABLED - The URL where ORY Oathkeeper can retrieve JSON Web Keys from for validating the JSON Web
    # Token. Usually something like "https://my-keys.com/.well-known/jwks.json". The response of that endpoint must
    # return a JSON Web Key Set (JWKS).
    jwks_urls:
      - https://my-website.com/.well-known/jwks.json
      - https://my-other-website.com/.well-known/jwks.json
      - file://path/to/local/jwks.json

    # Sets the strategy to be used to validate/match the scope. Supports "hierarchic", "exact", "wildcard", "none". Defaults
    # to "none".
    scope_strategy: none

  # Configures the noop authenticator
  noop:
    # Set enabled to true if the authenticator should be enabled and false to disable the authenticator. Defaults to false.
    enabled: true

  # Configures the oauth2_client_credentials authenticator
  oauth2_client_credentials:
    # Set enabled to true if the authenticator should be enabled and false to disable the authenticator. Defaults to false.
    enabled: true

    # REQUIRED IF ENABLED - The OAuth 2.0 Token Endpoint that will be used to validate the client credentials.
    token_url: https://my-website.com/oauth2/token

  # Configures the oauth2_introspection authenticator
  oauth2_introspection:
    # Set enabled to true if the authenticator should be enabled and false to disable the authenticator. Defaults to false.
    enabled: true

    # REQUIRED IF ENABLED - The OAuth 2.0 Token Introspection endpoint.
    introspection_url: https://my-website.com/oauth2/introspection

    # Sets the strategy to be used to validate/match the token scope. Supports "hierarchic", "exact", "wildcard", "none". Defaults
    # to "none".
    scope_strategy: exact

    # Enable pre-authorization in cases where the OAuth 2.0 Token Introspection endpoint is protected by OAuth 2.0 Bearer
    # Tokens that can be retrieved using the OAuth 2.0 Client Credentials grant.
    pre_authorization:
      # Enable pre-authorization. Defaults to false.
      enabled: true

      # REQUIRED IF ENABLED - The OAuth 2.0 Client ID to be used for the OAuth 2.0 Client Credentials Grant.
      client_id: some_id

      # REQUIRED IF ENABLED - The OAuth 2.0 Client Secret to be used for the OAuth 2.0 Client Credentials Grant.
      client_secret: some_secret

      # REQUIRED IF ENABLED - The OAuth 2.0 Scope to be requested during the OAuth 2.0 Client Credentials Grant.
      scope:
        - foo
        - bar

      # REQUIRED IF ENABLED - The OAuth 2.0 Token Endpoint where the OAuth 2.0 Client Credentials Grant will be performed.
      token_url: https://my-website.com/oauth2/token

  # Configures the unauthorized authenticator
  unauthorized:
    # Set enabled to true if the authenticator should be enabled and false to disable the authenticator. Defaults to false.
    enabled: true

# All authorizers can be configured under this configuration key
authorizers:
  # Configures the allow authorizer
  allow:
    # Set enabled to true if the authorizer should be enabled and false to disable the authorizer. Defaults to false.
    enabled: true

  # Configures the deny authorizer
  deny:
    # Set enabled to true if the authorizer should be enabled and false to disable the authorizer. Defaults to false.
    enabled: true

  # Configures the keto_engine_acp_ory authorizer
  keto_engine_acp_ory:
    # Set enabled to true if the authorizer should be enabled and false to disable the authorizer. Defaults to false.
    enabled: true
    # REQUIRED IF ENABLED - The base URL of ORY Keto, typically something like http(s)://<host>[:<port>]/
    base_url: http://my-keto/

# All mutators can be configured under this configuration key
mutators:
  # Configures the cookie mutator
  cookie:
    # Set enabled to true if the mutator should be enabled and false to disable the mutator. Defaults to false.
    enabled: true

  # Configures the header mutator
  header:
    # Set enabled to true if the mutator should be enabled and false to disable the mutator. Defaults to false.
    enabled: true

  # Configures the id_token mutator
  id_token:
    # Set enabled to true if the mutator should be enabled and false to disable the mutator. Defaults to false.
    enabled: true
    # REQUIRED IF ENABLED - Sets the "iss" value of the ID Token.
    issuer_url: https://my-oathkeeper/
    # REQUIRED IF ENABLED - Sets the URL where keys should be fetched from. Supports remote locations (http, https) as
    # well as local filesystem paths.
    jwks_url: https://fetch-keys/from/this/location.json
    # jwks_url: file:///from/this/absolute/location.json
    # jwks_url: file://../from/this/relative/location.json

    # Sets the time-to-live of the ID token. Defaults to one minute. Valid time units are: s (second), m (minute), h (hour).
    ttl: 60s

  # Configures the noop mutator
  noop:
    # Set enabled to true if the mutator should be enabled and false to disable the mutator. Defaults to false.
    enabled: true
```