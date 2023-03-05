# Docker - Plausible

> Plausible Analytics is a simple, open source, lightweight (< 1 KB) and privacy-friendly alternative to Google Analytics.

Installs [Plausible](https://plausible.io/) as Docker container. Based on plausible's [self-hosted documentation](https://plausible.io/docs/self-hosting).

## Requirements

Docker Engine, `docker` and `docker-compose` Python packages need to be installed on the host.
For example, use `geerlingguy.docker` and `geerlingguy.pip` to install.

## Role Variables

Most variables are counterparts of Plausible variables described in their [Documentation](https://plausible.io/docs/self-hosting-configuration).

Passwords and `plausible_secret_key` should be (at least) included in an **ansible vault**!

You should at least overwrite default values for some basic variables:

```yml
docker_user: root
plausible_url: "plausible.{{ inventory_hostname }}"
plausible_db_password: postgres
plausible_secret_key: replace-me
```

By default Plausible won't be installed, until you set `install_plausible` to true.
We set it per host to define, on which hosts we want to serve Plausible.

You may want to disable public registration. Possible values are

- `true`
- `false`
- `invite_only` (default))

```yml
plausible_disable_registration: invite_only
```

If you want Plausible to send emails through a [smtp relay server](https://plausible.io/docs/self-hosting-configuration#mailersmtp-setup) (use ansible vault):

```yml
plausible_mail: "plausible@{{ inventory_hostname }}"
plausible_use_smtp: true
plausible_smtp_host:
plausible_smtp_port:
plausible_smtp_user:
plausible_smtp_pwd:
plausible_smtp_ssl: true
```

If you want to use Google Search Console or Google Analytics import you need to follow the [Plausible Documentation](https://plausible.io/docs/self-hosting-configuration#google-api-integration) and change some variables:

```yml
plausible_use_googlesearchintegration: false
plausible_google_client_id:
plausible_google_client_secret:
```

Using Traefik? The following labels are passed to the frontend Plausible container:

```yml
    labels:
      traefik.enable: {{ plausible_use_traefik }}
      traefik.http.routers.plausible.rule: "Host(`{{ plausible_url }}`)"
      traefik.http.services.plausible.loadbalancer.server.port: "8000"
```

```yml
plausible_use_traefik: true
```

If you want to update Plausible, we just need to pull the new images:

```yml
plausible_pull_images: false
plausible_recreate: smart
```

If you want to uninstall Plausible (shutdown, delete container, delete configuration) just set `ìnstall_plausible` to false.

Additionally, you could remove the volumes. Be careful, it will delete all Plausible data!

```yml
plausible_remove_volumes: false # !! deletes data if true !!
```

Some variables to fit install to your environment:

```yml
docker_user: root
plausible_dir: /var/docker/plausible
plausible_stack_name: plausible
plausible_state: present
```

## Dependencies

None.

## Example Playbook

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

```yml
---

- name: Install Plausible Web Analytics
  hosts: docker
  become: true
  vars_files:
    - vars/docker_vars.yml # sets `docker_user`
    - vars/plausible_vars.yml
    - vars/plausible_vault.yml

  vars:
    pip_install_packages:
      - name: docker
      - name: docker-compose

  roles:
    - geerlingguy.pip
    - docker-plausible
```

## License

GPL-3.0

## Author Information

We are using this role to manage our own installation.
Nevertheless, you should understand it before you are using it.
Please consider to [donate to Plausible](https://github.com/sponsors/plausible).

Sebastian Buck
platomo
