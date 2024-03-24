<!-- markdownlint-disable MD013 MD041 -->
[![ansible-lint](https://github.com/sscheib/ansible-role-satellite_smart_proxies/actions/workflows/ansible-lint.yml/badge.svg)](https://github.com/sscheib/ansible-role-satellite_smart_proxies/actions/workflows/ansible-lint.yml) [![Publish to Ansible Galaxy](https://github.com/sscheib/ansible-role-satellite_smart_proxies/actions/workflows/release.yml/badge.svg)](https://github.com/sscheib/ansible-role-satellite_smart_proxies/actions/workflows/release.yml) [![markdown link check](https://github.com/sscheib/ansible-role-satellite_smart_proxies/actions/workflows/markdown-link-check.yml/badge.svg)](https://github.com/sscheib/ansible-role-satellite_smart_proxies/actions/workflows/markdown-link-check.yml) [![markdownlint](https://github.com/sscheib/ansible-role-satellite_smart_proxies/actions/workflows/markdownlint.yml/badge.svg)](https://github.com/sscheib/ansible-role-satellite_smart_proxies/actions/workflows/markdownlint.yml) [![pyspelling](https://github.com/sscheib/ansible-role-satellite_smart_proxies/actions/workflows/pyspelling.yml/badge.svg)](https://github.com/sscheib/ansible-role-satellite_smart_proxies/actions/workflows/pyspelling.yml) [![pre-commit](https://img.shields.io/badge/pre--commit-enabled-brightgreen?logo=pre-commit&logoColor=white)](https://github.com/pre-commit/pre-commit) [![Renovate enabled](https://img.shields.io/badge/renovate-enabled-brightgreen.svg)](https://www.mend.io/renovate/)
<!-- markdownlint-disable MD013 MD041 -->

# satellite_smart_proxies

This role allows to create, delete and modify Satellite Smart Proxy associations. It does **not** install Red Hat Satellite Capsule servers. It allows managing the Smart Proxy
functionality of the Satellite's builtin Capsule or any other installed Capsule.

This is useful if you want to assign or remove your Satellite/Capsule to/from a Organization or to/from a Location.

It makes use of the Red Hat certified collection [`redhat.satellite`](https://console.redhat.com/ansible/automation-hub/repo/published/redhat/satellite/docs/), specifically
of the module [`redhat.satellite.smart_proxy`](https://console.redhat.com/ansible/automation-hub/repo/published/redhat/satellite/content/module/smart_proxy/).

To use the certified collection `redhat.satellite` you need to be a Red Hat subscriber. If you don't own any subscriptions, you can make use of
[Red Hat's Developer Subscription](https://developers.redhat.com/articles/faqs-no-cost-red-hat-enterprise-linux) which is provided at no cost by Red Hat.

You can also make use of the upstream collection [`theforeman.foreman`](https://docs.ansible.com/ansible/latest/collections/theforeman/foreman/index.html), but you'd need to
change the module names from `redhat.satellite` to `theforeman.foreman` - but I have *not* tested this.

This role supports all available module options of `redhat.satellite.smart_proxy` at the time of this writing (23.03.2024).

## Requirements

This role requires the collection `redhat.satellite`. It is specified via `collections/requirements.yml`.

## Role Variables

| variable                                     | default                      | required | description                                                                    |
| :---------------------------------           | :--------------------------- | :------- | :----------------------------------------------------------------------------- |
| `sat_smart_proxies`                          | unset                        | true     | Smart Proxies to create/update/delete                                          |
| `satellite_username`                         | unset                        | true[^1] | Username to authenticate with against the Satellite API                        |
| `satellite_password`                         | unset                        | true[^1] | Password of the user to authenticate with against the Satellite API            |
| `satellite_server_url`                       | unset                        | true[^1] | URL to the Satellite API (including http/s://)                                 |
| `satellite_validate_certs`                   | `true`                       | false    | Whether to validate certificates when connecting to the Satellite API          |
| `sat_quiet_assert`                           | `true`                       | false    | Whether to quiet assert                                                        |

[^1]: This role also supports passing the Satellite URL, username and password via environment variables. Please check `vars/main.yml` for the exact variables to use.

## Variable `sat_smart_proxies`

An extended example of only the `sat_smart_proxies` variable is illustrated down below:

```yaml
---
sat_smart_proxies:
  - name: 'satellite.example.com
    url: 'https://satellite.example.com:9090'
    locations:
      - 'loc-default'
      - 'loc-special'
    organizations:
      - 'org-default'
      - 'org-special'

  - name: 'capsule.example.com
    url: 'https://capsule.example.com:9090'
    locations:
      - 'loc-default'
      - 'loc-special'
    organizations:
      - 'org-default'
      - 'org-special'
    lifecycle_environments:
      - 'lce-default-dev'
      - 'lce-default-prod'
    download_policy: 'on_demand'
...
```

The only required attributes are `name` and `url`. Everything else can be mixed and matched. A validation of the attributes is only performed on `name` and `url`.

## Dependencies

None

## Example Playbook

```yaml
---
- name: 'Configure Smart Proxy functionality'
  hosts: 'all'
  gather_facts: false
  roles:
    - role: 'satellite_smart_proxies'
      vars:
        satellite_server_url: 'https://satellite.example.com'
        satellite_username: 'steffen'
        satellite_password: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          [..]
        satellite_validate_certs: true
        sat_quiet_assert: false
        sat_smart_proxies:
          - name: 'satellite.example.com
            url: 'https://satellite.example.com:9090'
            locations:
              - 'loc-default'
              - 'loc-special'
            organizations:
              - 'org-default'
              - 'org-special'

          - name: 'capsule.example.com
            url: 'https://capsule.example.com:9090'
            locations:
              - 'loc-default'
              - 'loc-special'
            organizations:
              - 'org-default'
              - 'org-special'
            lifecycle_environments:
              - 'lce-default-dev'
              - 'lce-default-prod'
            download_policy: 'on_demand'
...
```

## License

[`GPL-2.0-or-later`](LICENSE)
