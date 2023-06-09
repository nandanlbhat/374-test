redhat.satellite.sync_plans
=============================

This role defines Sync Plans.

Role Variables
--------------

This role supports the [Common Role Variables](https://github.com/theforeman/foreman-ansible-modules/blob/develop/README.md#common-role-variables).

- `satellite_sync_plans`: List of sync plans to create. Each sync plan is represented as a dictionary which specifies the `name` of the sync plan and the `products` assigned to the sync plan. It also specifies the `interval` which can be 'hourly', 'daily', 'weekly', or 'custom cron'. In case the 'custom cron' `interval` is used, it should also specify the `cron_expression`. Finally the sync plan should have a `sync_date` which specifies the first time that the sync plan will run. Optionally the sync plan can be enabled and disabled using the `enabled` parameter, and its state can be managed using `state`.

```yaml
satellite_sync_plans:
  - name: Weekly Sync
    interval: weekly
    sync_date: 2020-11-07 00:00:00 UTC
    products:
      - Red Hat Enterprise Linux Server
      - Red Hat Software Collections (for RHEL Server)
      - Red Hat Enterprise Linux for x86_64
      - CentOS 8
      - Debian 10
  - name: Monthly Foreman Client Sync
    interval: custom cron
    cron_expression: 0 6 8 * *
    sync_date: 2020-11-08 00:06:00 UTC
    products:
      - Foreman Client
  - name: Weeky Ubuntu Sync (disabled)
    interval: weekly
    sync_date: 2020-11-07 00:00:00 UTC
    products:
      - Ubuntu 22.04
    enabled: false
```

Example Playbooks
-----------------

Create two sync plans:

```yaml
- hosts: localhost
  roles:
    - role: redhat.satellite.sync_plans
      vars:
        satellite_server_url: https://satellite.example.com
        satellite_username: "admin"
        satellite_password: "changeme"
        satellite_organization: "Default Organization"
        satellite_sync_plans:
          - name: Weekly Sync
            interval: weekly
            sync_date: 2020-11-07 00:00:00 UTC
            products:
              - Red Hat Enterprise Linux Server
              - Red Hat Enterprise Linux for x86_64
          - name: Daily Sync
            interval: daily
            sync_date: 2020-11-08 00:00:00 UTC
            products:
              - Red Hat Software Collections (for RHEL Server)
```
 
Create a single sync plan which has all defined products (those defined in the `satellite_products` dictionary in ansible vars, for example as defined in the role documentation for [redhat.satellite.repositories](https://github.com/theforeman/foreman-ansible-modules/tree/develop/roles/repositories#role-variables)) assigned to it:

```yaml
- hosts: localhost
  roles:
    - role: redhat.satellite.sync_plans
      vars:
        satellite_server_url: https://satellite.example.com
        satellite_username: "admin"
        satellite_password: "changeme"
        satellite_organization: "Default Organization"
        satellite_sync_plans:
          - name: Weekly Sync
            interval: weekly
            sync_date: 2020-11-07 00:00:00 UTC
            products: "{{ satellite_products | map(attribute='name') | list }}"
```

The above example assumes that a yaml dictionary `satellite_products` is already defined in Ansible variables. It uses yaml methods to select the name of each product from that dictionary, convert them all to a list, and pass that list to the definition of the sync plan.
