[![Ansible Galaxy](https://raw.githubusercontent.com/roles-ansible/ansible_role_gitea/main/.github/galaxy.svg?sanitize=true)](https://galaxy.ansible.com/do1jlr/gitea) [![MIT License](https://raw.githubusercontent.com/roles-ansible/ansible_role_gitea/main/.github/license.svg?sanitize=true)](https://github.com/roles-ansible/ansible_role_gitea/blob/main/LICENSE)

 ansible role gitea
===================

This role installs and manages [gitea](https://gitea.io) - Git with a cup of tea. A painless self-hosted Git service. Gitea is a community managed lightweight code hosting solution written in Go.
[Source code & screenshots](https://github.com/go-gitea/gitea).

## Sample example of use in a playbook

The following code has been tested with Debian 8, it should work on Ubuntu as well.

```yaml
- name: "Install gitea"
  hosts: all
  vars:
    gitea_user: "gitea"
    gitea_home: "/var/lib/gitea"
    # To limit your users to 30 repos
    gitea_user_repo_limit: 30
    # Don't use a public CDN for frontend assets
    gitea_offline_mode: true

    # Some 'rendering' options for your URLs
    gitea_http_domain: git.yourdomain.fr
    gitea_root_url: https://git.yourdomain.fr

    # Here we assume we are behind a reverse proxy that will
    # handle https for us, so we bind on localhost:3000 using HTTP
    gitea_protocol: http
    gitea_http_listen: 127.0.0.1
    gitea_http_port: 3000

    # SSH server configuration
    gitea_ssh_listen: 0.0.0.0
    gitea_ssh_port: 2222
    # For URLs rendering again
    gitea_ssh_domain: git.yourdomain.fr
    gitea_start_ssh: true

    gitea_secret_key: 3sp00ky5me
    gitea_disable_gravatar: true
    # To make at least your first user register
    gitea_disable_registration: false
    gitea_require_signin: true
    gitea_enable_captcha: true

    gitea_show_user_email: false
  roles:
    - gitea
```

 Variables
-----------
Here is a deeper insight into the variables of this gitea role. For the exact function of some variables and the possibility to add more options we recommend a look at the config cheat sheet.  For the exact function of some variables and the possibility to add more options we recommend a look at this [config cheat sheet](https://docs.gitea.io/en-us/config-cheat-sheet/).

### gitea version
| variable name | default value | description |
| ------------- | ------------- | ----------- |
| `gitea_version` | *(see [defaults/main.yml](defaults/main.yml#L3))* | The gitea version this role shoud install |
| `gitea_version_check` | `true` | Check if installed version != `gitea_version` before initiating binary download |
| `gitea_dl_url` | *(see [defaults/main.yml](defaults/main.yml#L5))* | The path from where this role downloads the gitea binary |
| `gitea_gpg_key` | `7C9E68152594688862D62AF62D9AE806EC1592E2` | the gpg key the gitea binary is signed with |
| `gitea_gpg_server` | `hkp://keyserver.ubuntu.com:80` | A gpg key server where this role can download the gpg key |

### gitea in the linux world
| variable name | default value | description |
| ------------- | ------------- | ----------- |
| `gitea_user` | `gitea ` | UNIX user used by Gitea |
| `gitea_group` | `gitea` | UNIX group used by Gitea |
| `gitea_home` | `/var/lib/gitea` | Base directory to work |
| `gitea_shell` | `/bin/false` | UNIX shell used by gitea. Set it to `/bin/bash` if you don't use the gitea built-in ssh server. |

### Look and feel
| variable name | default value | description |
| ------------- | ------------- | ----------- |
| `gitea_app_name` | `Gitea` | Displayed application name |
| `gitea_repository_root` | `{{ gitea_home }}/repos` | The path where all gitea repositorys are stored |
| `gitea_show_user_email` | `false` | Do you want to display email addresses ? (true/false) |
| `gitea_disable_gravatar` | `true` | Do you want to disable Gravatar ? (privacy and so on) (true/false) |
| `gitea_disable_registration` | `false` | Do you want to disable user registration? (true/false) |
| `gitea_register_email_confirm` | `false` | Enable this to ask for mail confirmation of registration. Requires `gitea_mailer_enabled` to be enabled. |
| `gitea_offline_mode` | `true` | Disables use of CDN for static files and Gravatar for profile pictures. (true/false) |
| `gitea_only_allow_external_registration` | `false` | Set to true to force registration only using third-party services (true/false) |
| `gitea_show_registration_button` | `true` | Here you can hide the registration button. This will not disable registration! (true/false)|
| `gitea_require_signin` | `true` | Do you require a signin to see repo's (even public ones)? (true/false)|
| `gitea_enable_captcha` | `true` | Do you want to enable captcha's ? (true/false)|
| `gitea_enable_notify_mail` | `false` | Enable this to send e-mail to watchers of a repository when something happens, like creating issues (true/false) |
| `gitea_auto_watch_new_repos` | `true` | Enable this to let all organisation users watch new repos when they are created (true/false) |
| `gitea_themes` | `gitea,arc-green` | List of enabled themes |
| `gitea_theme_default` | `gitea` | Default theme |


### Security
| variable name | default value | description |
| ------------- | ------------- | ----------- |
| `gitea_secret_key` | `''` | Global secret key. Will be autogenerated if not defined. Should be unique. |
| `gitea_internal_token` | `''` | Internal API token. Will be autogenerated if not defined. Should be unique. |
| `gitea_disable_git_hooks` | `true` | Set to false to enable users with git hook privilege to create custom git hooks. Can be dangerous. |
| `gitea_user_repo_limit` | `-1` | Limit how many repos a user can have *(`-1` for unlimited)* |
| `gitea_lfs_secret` | `''` < JWT secret for remote LFS usage. Can be generated with ``gitea generate secret JWT_SECRET``. Will be autogenerated if not defined |
|
| `gitea_oauth2_jwt_secret` | `''` | Oauth2 JWT secret. Can be generated with ``gitea generate secret JWT_SECRET``. Will be autogenerated if not defined. |




### HTTP configuration

* `gitea_http_domain`: HTTP domain (displayed in your clone URLs, just the domain like git.foo.fr)
* `gitea_root_url`: Root URL used to access your web app (full URL)
* `gitea_protocol`: Listening protocol (http/https)
* `gitea_http_listen`: Bind address
* `gitea_http_port`: Bind port (redirect from `80` will be activated if value is `443` - Default: `3000`)
* `gitea_disable_http_git`: Disable the use of Git over HTTP ? (true/false)
* `gitea_http_letsencrypt_mail` Enable Let`s Encrypt if a email address is given

### SSH configuration

* `gitea_ssh_listen`: Bind address for the SSH server
* `gitea_ssh_domain`: SSH domain (displayed in your clone URLs)
* `gitea_start_ssh`: Do you want to start a built-in SSH server ? (true/false)
* `gitea_ssh_port`: SSH bind port

### Database configuration

* `gitea_db_type`: Database type, can be `mysql`, `postgres` or `sqlite3`
* `gitea_db_host`: Database host string `host:port` or `/run/postgresql/` when connectiong to postgres via local unix socket (peer authentication)
* `gitea_db_name`: Database name
* `gitea_db_user`: Database username
* `gitea_db_password`: Database password
* `gitea_db_ssl`: Use SSL ? (postgres only!). Can be `require`, `disable`, `verify-ca` or `verify-full`
* `gitea_db_path`: DB path, if you use `sqlite3`. The default is good enough to work though.

### Mailer configuration

* `gitea_mailer_enabled`: Whether to enable the mailer. Default: `false`
* `gitea_mailer_skip_verify`: Skip SMTP TLS certificate verification (true/false)
* `gitea_mailer_tls_enabled`: Enable TLS for SMTP connections (true/false)
* `gitea_mailer_host`: SMTP server hostname and port
* `gitea_mailer_user`: SMTP server username
* `gitea_mailer_password`: SMTP server password
* `gitea_mailer_from`: Sender mail address
* `gitea_subject_prefix`: Prefix to be placed before e-mail subject lines (Default: ``)
* `gitea_enable_notify_mail`:  Whether e-mail should be send to watchers of a repository when something happens. Default: `false`

### LFS configuration

* `gitea_lfs_enabled`: Enable GIT LFS *(git large file storeage: [git-lfs](https://git-lfs.github.com/))*. Default: `false`
* `gitea_lfs_content_path`: path where the lfs files are stored

### Log configuration
* `gitea_log_systemd` Disable logging into `file`, use systemd-journald
* `gitea_log_only_warning` Log only warnings or above, no http access or sql logging (Default: `true`)

### Fail2Ban configuration

If enabled, this will deploy a fail2ban filter and jail config for Gitea as described in the [Gitea Documentation](https://docs.gitea.io/en-us/fail2ban-setup/).

As this will only deploy config files, fail2ban already has to be installed or otherwise the role will fail.

* `gitea_fail2ban_enabled`: Whether to deploy the fail2ban config or not
* `gitea_fail2ban_jail_maxretry`: fail2ban jail `maxretry` setting. Default: `10`
* `gitea_fail2ban_jail_findtime`: fail2ban jail `findtime` setting. Default: `3600`
* `gitea_fail2ban_jail_bantime`: fail2ban jail `bantime` setting. Default: `900`
* `gitea_fail2ban_jail_action`: fail2ban jail `action` setting. Default: `iptables-allports`

### Oauth2 provider configuration

* `gitea_oauth2_enabled`: Enable the Oauth2 provider (true/false)


### Metrics endpoint configuration

* `gitea_metrics_enabled`: Enable the metrics endpoint
* `gitea_metrics_token`: Bearer token for the Prometheus scrape job

### Repository Indexer configuration

* `gitea_repo_indexer_enabled`: Whether to enable the repository indexer (code search). Default: `false`
* `gitea_repo_indexer_include`: Glob patterns to include in the index (comma-separated list). Default: `""` (all files)
* `gitea_repo_indexer_exclude`: Glob patterns to exclude from the index (comma-separated list). Default: `""` (no files)
* `gitea_repo_exclude_vendored`: Exclude vendored files from the index. Default: `true`
* `gitea_repo_indexer_max_file_size`: Maximum size of files to be indexed (in bytes). Default: `1048576` (1 MB)

### backup on upgrade
* `gitea_backup_on_upgrade`: Optionally a backup can be created with every update of gitea. Default: `false`
* `gitea_backup_location`: Where to store the gitea backup if one is created with this role. Default: `{{ gitea_home }}/backups/`

* `gitea_systemd_cap_net_bind_service`: Adds `AmbientCapabilities=CAP_NET_BIND_SERVICE` to systemd service file
* `gitea_extra_config`: Additional configuration
## Contributing
Don't hesitate to create a pull request, and when in doubt you can reach me on
Mastodon [@l3d@chaos.social](https://chaos.social/@l3d).

I'm happy to fix any issue that's been opened, or even better, review your pull requests :)

## Testing
Testing uses [molecule](https://molecule.readthedocs.io/en/stable-1.22/usage.html). To start the
tests, install the dependencies first. I would recommend you use [a virtual env](https://virtualenv.pypa.io/en/latest/) for that but who am I to tell you what to do.

```
pip install pew # install pew to manage the venvs
pew new ansible # create the venv
pip install -r requirements-travis.txt # install the requirements
molecule test # Run the actual tests
```

Note: you need Docker installed

### Known testing limitations
Currently it's mainly validating that the playbook runs, the lint is ok, and that kind of things.
Since it runs in Docker, we currently have no way to check if the service is actually launched by systemd
and so on. This has to be worked on.

## License
```
Copyright (c) 2019 - today L3D <l3d@c3woc.de>
Copyright (c) 2019 - 2021 Thomas Maurice

Redistribution and use in source and binary forms, with or without modification, are permitted provided that the following conditions are met:

1. Redistributions of source code must retain the above copyright notice, this list of conditions and the following disclaimer.

2. Redistributions in binary form must reproduce the above copyright notice, this list of conditions and the following disclaimer in the documentation and/or other materials provided with the distribution.

3. Neither the name of the copyright holder nor the names of its contributors may be used to endorse or promote products derived from this software without specific prior written permission.

THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS" AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT HOLDER OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
```
