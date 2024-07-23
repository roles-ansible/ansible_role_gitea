[![Ansible Galaxy](https://ansible.l3d.space/svg/l3d.gitea.svg)](https://galaxy.ansible.com/ui/standalone/roles/roles-ansible/gitea/)
[![BSD-3 Clause](https://ansible.l3d.space/svg/l3d.gitea_license.svg)](LICENSE)
[![Maintainance](https://ansible.l3d.space/svg/l3d.gitea_maintainance.svg)](https://ansible.l3d.space/#l3d.gitea)

 ansible role gitea/forgejo
============================

This role installs and manages [gitea](https://gitea.io) or [forgejo](https://forgejo.org). A painless self-hosted Git service. Gitea is a community managed lightweight code hosting solution written in Go. Forgejo is a fork of it.
[Source code & screenshots gitea](https://github.com/go-gitea/gitea).
[Source code forgejo](https://code.forgejo.org/forgejo/forgejo).
This role is also Part of the Ansible-Collection [l3d.git](https://galaxy.ansible.com/l3d/git). [![l3d.git](https://ansible.l3d.space/svg/l3d.git_ansible-collection_collection.svg)](https://github.com/roles-ansible/ansible_collection_git.git).

## Mirrors
The role is mirrored to:
+ Github: [github.com/roles-ansible/ansible_role_gitea](https://github.com/roles-ansible/ansible_role_gitea.git)
+ Gitea: [git.l3d.ch/ansible/ansible_role_gitea](https://git.l3d.ch/ansible/ansible_role_gitea.git)
More about it at [ansible.l3d.space](https://ansible.l3d.space/#l3d.gitea)

## Sample Usage in a playbook

The following code has been tested with the latest Debian Stable, it should work on Ubuntu and RedHat as well.

```yaml
# ansible-galaxy role install l3d.gitea

- name: "Install gitea"
  hosts: git.example.com
  roles:
    - {role: l3d.gitea, tags: gitea}
  vars:
    # Here we assume we are behind a reverse proxy that will
    # handle https for us, so we bind on localhost:3000 using HTTP
    # see https://docs.gitea.io/en-us/reverse-proxies/#nginx
    gitea_fqdn: 'git.example.com'
    gitea_root_url: 'https://git.example.com'
    gitea_protocol: http
    gitea_start_ssh: true
```

## Choosing between Gitea's built-in SSH and host SSH Server

Gitea has a built-in SSH server which is running on port 2222 (to not conflict with the host SSH server which usually running on port 22).
This one is used by default in this role and results in a SSH clone URL of `gitea@<fqdn>:2222:<user>/<repo>.git` because `gitea` is the default `RUN_AS` user.

Often enough, one wants to have a "clean" SSH URL like `git@<fqdn>:<user>/<repo>.git`.
This is possible by using the host SSH server with the following variable configuration:

```yaml
gitea_ssh_port: 22 # assuming the host SSH server is running on port 22
gitea_user: git # otherwise there will be permission issues
gitea_start_ssh: false # to not start the built-in SSH server
```

The above configuration works out of the box for new installations.
When migrating from a running instance with existing SSH keys from the built-in SSH server to the host SSH server, you need to make sure that the host SSH server is running and that the `gitea_user` has the necessary permissions to access the repository data and the keys (stored in `<gitea_home>/.ssh/`)

NB: To use `git@` as described above, `gitea_user` must be `git` and it does not suffice to set `gitea_ssh_user: git`.
See [this issue](https://github.com/go-gitea/gitea/issues/28563) for more information..

 Variables
-----------
Here is a deeper insight into the variables of this gitea role. For the exact function of some variables and the possibility to add more options we recommend a look at this [config cheat sheet](https://docs.gitea.com/administration/config-cheat-sheet).

### Chose between gitea and forgejo
There is a fork of gitea called forgejo. Why? Read the [forgejo FAQ](https://forgejo.org/faq/).
You have the option to choose between [gitea](https://gitea.io) and [forgejo](https://forgejo.org) by modifying the ``gitea_fork`` variable.
| variable name | default value | description |
| ------------- | ------------- | ----------- |
| `gitea_fork`  | `gitea`       | optional choose to install forgejo instead of gitea by setting this value to `forgejo`. |

### gitea update mechanism
To determine which gitea version to install, you can choose between two variants.
Either you define exactly which release you install. Or you use the option ``latest`` to always install the latest release from the [gitea releases](https://github.com/go-gitea/gitea/releases/latest).

### Forgejo update mechanism
It is advisable to define exactly which Forgejo release you want to install. See [Forgejo releases](https://forgejo.org/releases/) for the correct value to use in `gitea_version` eg `v1.21.5`.

This is because the Forgejo project maintains both `stable` and `old stable` releases and the `latest` tag will refer to the *most recent release* regardless of whether it is `stable` or `old stable`. This can lead to a situation where `latest` refers to an *older release* than the version you have installed.

### gitea update
| variable name | default value | description |
| ------------- | ------------- | ----------- |
| `gitea_version` | `latest` | Define either the exact release to install *(eg. `1.16.0`)* or use ``latest`` *(default)* to install the latest release. |
| `gitea_version_check` | `true` | Check if installed version != `gitea_version` before initiating binary download |
| `gitea_gpg_key` | `7C9E68152594688862D62AF62D9AE806EC1592E2` | the gpg key the gitea binary is signed with |
| `gitea_forgejo_gpg_key` | `EB114F5E6C0DC2BCDD183550A4B61A2DC5923710` | the gpg key the forgejo binary is signed with |
| `gitea_gpg_server` | `hkps://keys.openpgp.org` | A gpg key server where this role can download the gpg key |
| `gitea_backup_on_upgrade` | `false` | Optionally a backup can be created with every update of gitea. |
| `gitea_backup_location` | `{{ gitea_home }}/backups/` | Where to store the gitea backup if one is created with this role. |
| `submodules_versioncheck` | `false` | a simple version check that can prevent you from accidentally running an older version of this role. *(recomended)* |

### gitea in the linux world
| variable name | default value | description |
| ------------- | ------------- | ----------- |
| `gitea_group` | `gitea` | Primary UNIX group used by Gitea |
| `gitea_groups` | null | Optionally a list of secondary UNIX groups used by Gitea |
| `gitea_home` | `/var/lib/gitea` | Base directory to work |
| `gitea_user_home` | `{{ gitea_home }}` | home of gitea user |
| `gitea_executable_path` | `/usr/local/bin/gitea` | Path for gitea executable |
| `gitea_forgejo_executable_path` | `/usr/local/bin/forgejo` | Path for forgejo executable |
| `gitea_configuration_path` | `/etc/gitea` | Where to put the gitea.ini config |
| `gitea_shell` | `/bin/false` | UNIX shell used by gitea. Set it to `/bin/bash` if you don't use the gitea built-in ssh server. |
| `gitea_systemd_cap_net_bind_service` | `false` | Adds `AmbientCapabilities=CAP_NET_BIND_SERVICE` to systemd service file |

### Overall ([DEFAULT](https://docs.gitea.com/administration/config-cheat-sheet#overall-default))
| variable name | default value | description |
| ------------- | ------------- | ----------- |
| `gitea_app_name` | `Gitea` | Displayed application name |
| `gitea_user` | `gitea ` | UNIX user used by Gitea |
| `gitea_run_mode`| `prod`| Application run mode, affects performance and debugging. Either “dev”, “prod” or “test”. |
| `gitea_fqdn` | `localhost` | Base FQDN for the installation, used as default for other variables. Set it to the FQDN where you can reach your gitea server |

### Repository ([repository](https://docs.gitea.com/administration/config-cheat-sheet#repository-repository))
| variable name | default value | description |
| ------------- | ------------- | ----------- |
| `gitea_default_branch` | `main` | Default branch name of all repositories. |
| `gitea_default_private` | `last` | Default private when creating a new repository. [`last`, `private`, `public`] |
| `gitea_default_repo_units` | *(see defaults)* | Comma separated list of default repo units. See official docs for more |
| `gitea_disabled_repo_units` | | Comma separated list of globally disabled repo units. |
| `gitea_disable_http_git` | `false` | Disable the ability to interact with repositories over the HTTP protocol. (true/false) |
| `gitea_disable_stars` | `false` | Disable stars feature. |
| `gitea_enable_push_create_org` | `false` | Allow users to push local repositories to Gitea and have them automatically created for an org. |
| `gitea_enable_push_create_user` | `false` | Allow users to push local repositories to Gitea and have them automatically created for an user. |
| `gitea_force_private` | `false` | Force every new repository to be private. |
| `gitea_user_repo_limit` | `-1` | Limit how many repos a user can have *(`-1` for unlimited)* |
| `gitea_repository_root` | `{{ gitea_home }}/repos` |  Root path for storing all repository data. It must be an absolute path. |
| `gitea_repository_extra_config` | | you can use this variable to pass additional config parameters in the `[repository]` section of the config. |

### Repository - Upload ([repository.upload](https://docs.gitea.io/en-us/administration/config-cheat-sheet#repository---upload-repositoryupload))
| variable name | default value | description |
| ------------- | ------------- | ----------- |
| `gitea_repository_upload_enabled` | `true` | Whether repository file uploads are enabled |
| `gitea_repository_upload_max_size` | `4` | Max size of each file in megabytes. |
| `gitea_repository_upload_extra_config` | | you can use this variable to pass additional config parameters in the `[repository.upload]` section of the config. |

### Repository - Signing ([repository.signing](https://docs.gitea.com/administration/config-cheat-sheet#repository---signing-repositorysigning))
| variable name | default value | description |
| ------------- | ------------- | ----------- |
| `gitea_enable_repo_signing_options` | `false` | Allow to configure repo signing options |
| `gitea_repo_signing_key` | `default` | Key to sign with. |
| `gitea_repo_signing_name` | | if a KEYID is provided as the `gitea_repo_signing_key`, use these as the Name and Email address of the signer. |
| `gitea_repo_signing_email` | | if a KEYID is provided as the `gitea_repo_signing_key`, use these as the Name and Email address of the signer. |
| `gitea_repo_initial_commit` | `always` | Sign initial commit. |
| `gitea_repo_default_trust_model` | `collaborator` | The default trust model used for verifying commits. |
| `gitea_repo_wiki` | `never` |  Sign commits to wiki. |
| `gitea_repo_crud_actions` | *(see defaults)* | Sign CRUD actions. |
| `gitea_repo_merges` | *(see defaults)* | Sign merges. |
| `gitea_enable_repo_signing_extra` | | you can use this variable to pass additional config parameters in the `[repository.signing]` section of the config. |

### CORS ([cors](https://docs.gitea.com/administration/config-cheat-sheet#cors-cors))
| variable name | default value | description |
| ------------- | ------------- | ----------- |
| `gitea_enable_cors` | `false` | enable cors headers (disabled by default) |
| `gitea_cors_scheme` | `http` | scheme of allowed requests |
| `gitea_cors_allow_domain` | `*` | list of requesting domains that are allowed |
| `gitea_cors_allow_subdomain` | `false` |allow subdomains of headers listed above to request |
| `gitea_cors_methods` | *(see defaults)* | list of methods allowed to request |
| `gitea_cors_max_age` | `10m` | max time to cache response |
| `gitea_cors_allow_credentials` | `false` | allow request with credentials |
| `gitea_cors_headers` | `Content-Type,User-Agent` | additional headers that are permitted in requests |
| `gitea_cors_x_frame_options` | `SAMEORIGIN` |  Set the `X-Frame-Options` header value. |
| `gitea_cors_extra` | | you can use this variable to pass additional config parameters in the `[cors]` section of the config. |

### UI ([ui](https://docs.gitea.com/administration/config-cheat-sheet#ui-ui))
| variable name | default value | description |
| ------------- | ------------- | ----------- |
| `gitea_show_user_email` | `false` | Do you want to display email addresses ? (true/false) |
| `gitea_theme_default` | `gitea-auto` or `forgejo-auto` | Default theme |
| `gitea_themes` | (See `defaults/gitea.yml` or `defaults/forgejo.yml`)| List of enabled themes |
| `gitea_ui_extra_config` | | you can use this variable to pass additional config parameters in the `[ui]` section of the config. |

### UI - Meta ([ui.meta](https://docs.gitea.com/administration/config-cheat-sheet#ui---metadata-uimeta))
| variable name | default value | description |
| ------------- | ------------- | ----------- |
| `gitea_ui_author` | *(see defaults)* | Author meta tag of the homepage. |
| `gitea_ui_description` | *(see defaults)* | Description meta tag of the homepage. |
| `gitea_ui_keywords` | *(see defaults)* | Keywords meta tag of the homepage |
| `gitea_ui_meta_extra_config` | | you can use this variable to pass additional config parameters in the `[ui.meta]` section of the config. |

### Server ([server](https://docs.gitea.com/administration/config-cheat-sheet#server-server))
| variable name | default value | description |
| ------------- | ------------- | ----------- |
| `gitea_protocol`| `http` | Listening protocol [http, https, fcgi, unix, fcgi+unix] |
| `gitea_http_domain` | `{{ gitea_fqdn }}` which is `localhost` | Domain name of this server. |
| `gitea_root_url` | `http://{{ gitea_fqdn }}:3000` | Root URL used to access your web app (full URL) |
| `gitea_http_listen` | `127.0.0.1` | HTTP listen address |
| `gitea_http_port` | `3000` | Bind port *(redirect from `80` will be activated if value is `443`)* |
| `gitea_start_ssh` | `true` | When enabled, use the built-in SSH server. |
| `gitea_ssh_domain` | `{{ gitea_fqdn }} ` |  Domain name of this server, used for displayed clone URL |
| `gitea_ssh_port` | `2222` | SSH port displayed in clone URL. |
| `gitea_ssh_listen` | `0.0.0.0` | Listen address for the built-in SSH server. |
| `gitea_offline_mode` | `true` | Disables use of CDN for static files and Gravatar for profile pictures. (true/false) |
| `gitea_landing_page` | `home` | Landing page for unauthenticated users |
| `gitea_lfs_server_enabled` | `false` | Enable GIT-LFS Support *(git large file storage: [git-lfs](https://git-lfs.github.com/))*. |
| `gitea_lfs_jwt_secret` |  | LFS authentication secret. Can be generated with ``gitea generate secret JWT_SECRET``. Will be autogenerated if not defined |
| `gitea_redirect_other_port` | `false` | If true and `gitea_protocol` is https, allows redirecting http requests on `gitea_port_to_redirect` to the https port Gitea listens on. |
| `gitea_port_to_redirect` | `80` | Port for the http redirection service to listen on, if enabled |
| `gitea_enable_tls_certs` | `false` | Write TLS Cert and Key Path to config file |
| `gitea_tls_cert_file` | `https/cert.pem` |  Cert file path used for HTTPS. |
| `gitea_tls_key_file` | `https/key.pem` | Key file path used for HTTPS. |
| `gitea_enable_acme` | `false` | Flag to enable automatic certificate management via an ACME capable CA Server. *(default is letsencrypt)* |
| `gitea_acme_url` | | The CA’s ACME directory URL |
| `gitea_acme_accepttos` | `false` | This is an explicit check that you accept the terms of service of the ACME provider. |
| `gitea_acme_directory` | `https` | Directory that the certificate manager will use to cache information such as certs and private keys. |
| `gitea_acme_email` | | Email used for the ACME registration |
| `gitea_acme_ca_root` | | The CA’s root certificate. If left empty, it defaults to using the system’s trust chain. |
| `gitea_server_extra_config` |  | you can use this variable to pass additional config parameters in the `[server]` section of the config. |

### Database ([database](https://docs.gitea.com/administration/config-cheat-sheet#database-database))
| variable name | default value | description |
| ------------- | ------------- | ----------- |
| `gitea_db_type` | `sqlite3` | The database type in use `[mysql, postgres, mssql, sqlite3]`. |
| `gitea_db_host` | `127.0.0.0:3306` | Database host address and port or absolute path for unix socket [mysql, postgres] (ex: `/var/run/mysqld/mysqld.sock`). |
| `gitea_db_name` | `root` | Database name |
| `gitea_db_user` | `gitea` | Database username |
| `gitea_db_password` | `lel` | Database password. **PLEASE CHANGE** |
| `gitea_db_ssl` | `disable` | Configure SSL only if your database type supports it. Have a look into the [config-cheat-sheet](https://docs.gitea.com/administration/config-cheat-sheet#database-database) for more detailed information |
| `gitea_db_path` | `{{ gitea_home }}/data/gitea.db` | DB path, if you use `sqlite3`. |
| `gitea_db_log_sql` | `false` | Log the executed SQL. |
| `gitea_database_extra_config` | | you can use this variable to pass additional config parameters in the `[database]` section of the config. |

### Indexer ([indexer](https://docs.gitea.com/administration/config-cheat-sheet#indexer-indexer))
| variable name | default value | description |
| ------------- | ------------- | ----------- |
| `gitea_repo_indexer_enabled` | `false` | Enables code search *(uses a lot of disk space, about 6 times more than the repository size).* |
| `gitea_repo_indexer_include` |  |Glob patterns to include in the index *(comma-separated list)*. An empty list means include all files. |
| `gitea_repo_indexer_exclude` |  | Glob patterns to exclude from the index (comma-separated list). |
| `gitea_repo_exclude_vendored` | `true` | Exclude vendored files from index. |
| `gitea_repo_indexer_max_file_size` | `1048576` | Maximum size in bytes of files to be indexed. |
| `gitea_indexer_extra_config` |  | you can use this variable to pass additional config parameters in the `[indexer]` section of the config. |
| `gitea_queue_issue_indexer_extra_config` | | | you can use this variable to pass additional config parameters in the `[queue.issue_indexer]` section of the config. |

### Security ([security](https://docs.gitea.com/administration/config-cheat-sheet#security-security))
| variable name | default value | description |
| ------------- | ------------- | ----------- |
| `gitea_secret_key` | | Global secret key. Will be autogenerated if not defined. Should be unique. |
| `gitea_disable_git_hooks` | `true` | Set to false to enable users with git hook privilege to create custom git hooks. Can be dangerous. |
| `gitea_disable_webhooks`  | `false` | Set to true to disable webhooks feature. |
| `gitea_internal_token` | | Internal API token. Will be autogenerated if not defined. Should be unique. |
| `gitea_password_check_pwn` | `false` | Check [HaveIBeenPwned](https://haveibeenpwned.com/Passwords) to see if a password has been exposed. |
| `gitea_security_extra_config` | | you can use this variable to pass additional config parameters in the `[security]` section of the config. |

### Service ([service](https://docs.gitea.com/administration/config-cheat-sheet#service-service))
| variable name | default value | description |
| ------------- | ------------- | ----------- |
| `gitea_disable_registration` | `false` | Do you want to disable user registration? (true/false) |
| `gitea_register_email_confirm` | `false` | Enable this to ask for mail confirmation of registration. Requires `gitea_mailer_enabled` to be enabled. |
| `gitea_require_signin` | `true` | Do you require a signin to see repo's (even public ones)? (true/false)|
| `gitea_default_keep_mail_private` | `true` | By default set users to keep their email address privat |
| `gitea_enable_captcha` | `true` | Do you want to enable captcha's ? (true/false)|
| `gitea_show_registration_button` | `true` | Here you can hide the registration button. This will not disable registration! (true/false)|
| `gitea_only_allow_external_registration` | `false` | Set to true to force registration only using third-party services (true/false) |
| `gitea_enable_notify_mail` | `false` | Enable this to send e-mail to watchers of a repository when something happens, like creating issues (true/false) |
| `gitea_auto_watch_new_repos` | `true` | Enable this to let all organisation users watch new repos when they are created (true/false) |
| `gitea_autowatch_on_change` | `true` | Enable this to make users watch a repository after their first commit to it (true/false) |
| `gitea_register_manual_confirm` | `false` | Enable this to manually confirm new registrations. Requires REGISTER_EMAIL_CONFIRM to be disabled. |
| `gitea_default_allow_create_organization` | `false` | Allow new users to create organizations by default (true/false) |
| `gitea_email_domain_allowlist` | | If non-empty, comma separated list of domain names that can only be used to register on this instance, wildcard is supported. |
| `gitea_default_user_visibility` | `public` | Set default visibility mode for users, either "public", "limited" or "private". |
| `gitea_default_org_visibility` | `public` | Set default visibility mode for organisations, either "public", "limited" or "private". |
| `gitea_allow_only_internal_registration` | `false` | Set to true to force registration only via Gitea. |
| `gitea_allow_only_external_registration` | `false` | Set to true to force registration only using third-party services. |
| `gitea_show_milestones_dashboard_page` | `true` | Enable this to show the milestones dashboard page - a view of all the user's milestones |
| `gitea_default_user_is_restricted` | `false` | Give new users restricted permissions by default (true/false) |
| `gitea_service_extra_config` | | you can use this variable to pass additional config parameters in the `[service]` section of the config. |

### Mailer ([mailer](https://docs.gitea.com/administration/config-cheat-sheet#mailer-mailer))
| variable name | default value | description |
| ------------- | ------------- | ----------- |
| `gitea_mailer_enabled` | `false` | Whether to enable the mailer. |
| `gitea_mailer_protocol` | `dummy` |Mail server protocol. One of “smtp”, “smtps”, “smtp+starttls”, “smtp+unix”, “sendmail”, “dummy”.|
| `gitea_mailer_smtp_addr` | | Mail server address. e.g. smtp.gmail.com. For smtp+unix, this should be a path to a unix socket instead. |
| `gitea_mailer_smtp_port` | | Mail server port |
| `gitea_mailer_use_client_cert` | `false` | Use client certificate for TLS/SSL. |
| `gitea_mailer_client_cert_file` | | Client certificate file. |
| `gitea_mailer_client_key_file` | | Client key file. |
| `gitea_mailer_force_trust_server_cert` | `false` | completely ignores server certificate validation errors. This option is unsafe. Consider adding the certificate to the system trust store instead. |
| `gitea_mailer_user` | | Username of mailing user (usually the sender’s e-mail address). |
| `gitea_mailer_password ` | |Password of mailing user. Use `your password` for quoting if you use special characters in the password. |
| `gitea_mailer_enable_helo` | `true` |Enable HELO operation. |
| `gitea_mailer_from` | `noreply@{{ gitea_http_domain }}` | Mail from address, RFC 5322. |
| `gitea_subject_prefix` | |Prefix to be placed before e-mail subject lines. |
| `gitea_mailer_send_as_plaintext` | `false` | Send mails only in plain text, without HTML alternative. |
| `gitea_mailer_extra_config` | | you can use this variable to pass additional config parameters in the `[mailer]` section of the config. |

### Session ([session](https://docs.gitea.com/administration/config-cheat-sheet#session-session))
| variable name | default value | description |
| ------------- | ------------- | ----------- |
| `gitea_session_provider` | `file` | Session engine provider |
| `gitea_session_extra_config` | | you can use this variable to pass additional config parameters in the `[session]` section of the config. |

### Picture ([picture](https://docs.gitea.com/administration/config-cheat-sheet#picture-picture))
| variable name | default value | description |
| ------------- | ------------- | ----------- |
| `gitea_picture_extra_config` | | you can use this variable to pass additional config parameters in the `[picture]` section of the config. |

### Issue and pull request attachments ([attachment](https://docs.gitea.com/administration/config-cheat-sheet#issue-and-pull-request-attachments-attachment))
| variable name | default value | description |
| ------------- | ------------- | ----------- |
| `attachment_enabled` | `true` | Whether issue and pull request attachments are enabled. |
| `gitea_attachment_types` | see Docs | Comma-separated list of allowed file extensions (`.zip,.txt`), mime types (`text/plain`) or wildcard type (`image/*`, `audio/*`, `video/*`). Empty value or `*/*` allows all types. |
| `gitea_attachment_max_size` | `4` | Maximum size (MB). |
| `gitea_attachment_extra_config` | | you can use this variable to pass additional config parameters in the `[attachment]` section of the config. |

### Log ([log](https://docs.gitea.com/administration/config-cheat-sheet#log-log))
| variable name | default value | description |
| ------------- | ------------- | ----------- |
| `gitea_log_systemd` | `false` | Disable logging into `file`, use systemd-journald |
| `gitea_log_level` | `Warn` | General log level. `[Trace, Debug, Info, Warn, Error, Critical, Fatal, None]` |
| `gitea_log_extra_config` | | you can use this variable to pass additional config parameters in the `[log]` section of the config. |

### Metrics ([metrics](https://docs.gitea.com/administration/config-cheat-sheet#metrics-metrics))
| variable name | default value | description |
| ------------- | ------------- | ----------- |
| `gitea_metrics_enabled`| `false` | Enable the metrics endpoint |
| `gitea_metrics_token`| | Bearer token for the Prometheus scrape job |
| `gitea_metrics_extra` | | you can use this variable to pass additional config parameters in the `[metrics]` section of the config. |

### OAuth2 ([oauth2](https://docs.gitea.com/administration/config-cheat-sheet#oauth2-oauth2))
| variable name | default value | description |
| ------------- | ------------- | ----------- |
| `gitea_oauth2_enabled` | `true` | Enable the Oauth2 provider (true/false) |
| `gitea_oauth2_jwt_secret` |  | Oauth2 JWT secret. Can be generated with ``gitea generate secret JWT_SECRET``. Will be autogenerated if not defined. |
| `gitea_oauth2_extra_config` |  | you can use this variable to pass additional config parameters in the `[oauth2]` section of the config. |

### Federation ([federation](https://docs.gitea.com/administration/config-cheat-sheet#federation-federation))
| variable name | default value | description |
| ------------- | ------------- | ----------- |
| `gitea_federation_enabled` | `false` | Enable/Disable federation capabilities |
| `gitea_federation_share_user_stats` | `false` | Enable/Disable user statistics for nodeinfo if federation is enabled |
| `gitea_federation_extra` | | you can use this variable to pass additional config parameters in the `[federation]` section of the config. |

### Packages ([packages](https://docs.gitea.com/administration/config-cheat-sheet#packages-packages))
| variable name | default value | description |
| ------------- | ------------- | ----------- |
| `gitea_packages_enabled` | `true` | Enable/Disable package registry capabilities |
| `gitea_packages_extra` | |you can use this variable to pass additional config parameters in the `[packages]` section of the config. |

### LFS ([lfs](https://docs.gitea.com/administration/config-cheat-sheet#lfs-lfs))
| variable name | default value | description |
| ------------- | ------------- | ----------- |
| `gitea_lfs_storage_type` | `local` | Storage type for lfs |
| `gitea_lfs_serve_direct` | `false` | Allows the storage driver to redirect to authenticated URLs to serve files directly. *(only Minio/S3)* |
| `gitea_lfs_content_path` | `{{ gitea_home }}/data/lfs` | Where to store LFS files |
| `gitea_lfs_extra` | | you can use this variable to pass additional config parameters in the `[lfs]` section of the config. |

### Actions ([actions](https://docs.gitea.com/administration/config-cheat-sheet#actions-actions))
| variable name | default value | description |
| ------------- | ------------- | ----------- |
| `gitea_actions_enabled` | `false` | Enable/Disable actions capabilities globaly. You may want to add `repo.actions` to `gitea_default_repo_units` to enable actions on all new repositories |
| `gitea_actions_default_actions_url` | `github` | Default address to get action plugins, e.g. the default value means downloading from `https://github.com/actions/checkout` for `uses: actions/checkout@v3` |
| `gitea_actions_extra` | | you can use this variable to pass additional config parameters in the `[actions]` section of the config. |

### Other ([other](https://docs.gitea.com/administration/config-cheat-sheet#other-other))
| variable name | default value | description |
| ------------- | ------------- | ----------- |
| `gitea_other_show_footer_version` | `true` | Show Gitea and Go version information in the footer. |
| `gitea_other_show_footer_template_load_time` | `true` | Show time of template execution in the footer. |
| `gitea_other_enable_sitemap` | `true` | Generate sitemap. |
| `gitea_other_enable_feed` | `true` | Enable/Disable RSS/Atom feed. |

### additional gitea config
| variable name | default value | description |
| ------------- | ------------- | ----------- |
| `gitea_extra_config` | | Additional gitea configuration. Have a look at the [config-cheat-sheet](https://docs.gitea.com/administration/config-cheat-sheet) before using it! |

### Fail2Ban configuration

If enabled, this will deploy a fail2ban filter and jail config for Gitea as described in the [Gitea Documentation](https://docs.gitea.io/en-us/fail2ban-setup/).

As this will only deploy config files, fail2ban already has to be installed or otherwise the role will fail.

| variable name | default value | description |
| ------------- | ------------- | ----------- |
| `gitea_fail2ban_enabled` | `false` | Whether to deploy the fail2ban config or not |
| `gitea_fail2ban_jail_maxretry` | `10` | fail2ban jail `maxretry` setting. |
| `gitea_fail2ban_jail_findtime` | `3600` | fail2ban jail `findtime` setting. |
| `gitea_fail2ban_jail_bantime` | `900` | fail2ban jail `bantime` setting. |
| `gitea_fail2ban_jail_action` | `iptables-allports` | fail2ban jail `action` setting. |

### local gitea Users
| variable | option | description |
| -------- | ------ | ----------- |
| ``gitea_users`` | | dict to create local gitea or forgejo users |
| | ``name`` | name for local gitea/forgejo user |
| | ``password`` | user for local git user |
| | ``email`` | email for local git user |
| | ``admin`` | give user admin permissions |
| | ``must_change_password`` | user should change password after first login |
| | ``state`` | set to ``absent`` to delete user |

### optional customisation
You can optionally customize your gitea using this ansible role. We got our information about customisation from [docs.gitea.io/en-us/customizing-gitea](https://docs.gitea.io/en-us/customizing-gitea/).
To deploy multiple files we created the ``gitea_custom_search`` variable, that can point to the path where you put the custom gitea files *( default ``"files/host_files/{{ inventory_hostname }}/gitea"``)*.

+ **LOGO**:
  - Set ``gitea_customize_logo`` to ``true``
  - We search for:
    * ``logo.svg`` - Used for favicon, site icon, app icon
    * ``logo.png`` - Used for Open Graph
    * ``favicon.png`` - Used as fallback for browsers that don’t support SVG favicons
    * ``apple-touch-icon.png`` - Used on iOS devices for bookmarks
  - We search in *(using [first_found](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/first_found_lookup.html))*:
    * ``{{ gitea_custom_search }}/gitea_logo/``
    * ``files/{{ inventory_hostname }}/gitea_logo/``
    * ``files/{{ gitea_http_domain }}/gitea_logo/``
    * ``files/gitea_logo/``
+ **FOOTER**:
  - Set ``gitea_customize_footer`` to ``true``
  - We Search using first_found in:
    * "{{ gitea_custom_search }}/gitea_footer/extra_links_footer.tmpl"
    * "files/{{ inventory_hostname }}/gitea_footer/extra_links_footer.tmpl"
    * "files/{{ gitea_http_domain }}/gitea_footer/extra_links_footer.tmpl"
    * 'files/gitea_footer/extra_links_footer.tmpl'
    * 'files/extra_links_footer.tmpl'
+ **CUSTOM FILES**:
  - Set ``gitea_customize_files`` to ``true``
  - Create a directory with the files you want to deploy.
  - Point ``gitea_customize_files_path`` to this directory. *(Default ``{{ gitea_custom_search }}/gitea_files/``)*
+ **CUSTOM THEMES**:
  - Set `gitea_custom_themes` to a list with URLs for custom theme CSS files. You usually want three individual files per theme. Example:
    ```yaml
    gitea_custom_themes:
      - https://example.com/theme-custom-auto.css
      - https://example.com/theme-custom-dark.css
      - https://example.com/theme-custom-light.css
    ```
  - Set `gitea_themes` variable and include the names of the new themes. To keep the existing ones, you need to pass all themes names, e.g. `auto,gitea,arc-green,<custom-auto>,<custom-light>,<custom-dark>`

## Requirements
This role uses the ``ansible.builtin`` and ``community.general`` ansible Collections. To download the latest forgejo/gitea release we use json_query. This requires ``jmespath`` to be available.

### Python packages
+ jmespath

### Galaxy Collections
+ community.general

### Example requirements Installation
```
ansible-galaxy collection install --update --role-file requirements.yml
pip3 install --update jmespath
```

## Contribute
Don't hesitate to create a pull request, and if in doubt you can reach me at
Mastodon [@l3d@chaos.social](https://chaos.social/@l3d).

I'll be happy to fix any issues you raise, or even better, review your pull requests :)

## History of this role
this ansible role was originally developed on [github.com/thomas-maurice/ansible-role-gitea](https://github.com/thomas-maurice/ansible-role-gitea.git). Since the role there has some problems like default values for the location of the gitea repositories and the merging of pull requests usually takes several months, a fork of the role was created that offers the same. Only tidier and with the claim to react faster to issues and pull requests. It is now Part of the [l3d.git](https://galaxy.ansible.com/l3d/git) Collection too.
