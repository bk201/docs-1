# Day-2 configuration change
Users can change some Harvester configurations after the initial deployment. This document shows how to do this.

## Bonding device change

Configuration: [`install.networks`](harvester-configuration.md#installnetworks)

1. Edit the file

    ```
    /oem/99_custom.yaml
    ```

2. Search for this section and edit `BONDING_SLAVE_0`, `BONDING_SLAVE_1`, etc.
    ```
        - path: /etc/sysconfig/network/ifcfg-harvester-mgmt
          permissions: 384
          owner: 0
          group: 0
          content: |+
            STARTMODE='onboot'
            BONDING_MASTER='yes'
            BOOTPROTO='dhcp'


            BONDING_SLAVE_0='ens6'

            BONDING_MODULE_OPTS='miimon=100 mode=balance-tlb '

            DHCLIENT_SET_DEFAULT_ROUTE='yes'

          encoding: ""
          ownerstring: ""
    ```

    !!!note
      - If the user adds more networks in `install.networks`, the `path` should be `/etc/sysconfig/network/ifcfg-{network-name}` accordingly.
      - A misconfiguration to a node could make the node unreachable, please check the configuration carefully.

3. Reboot the node.


## Static DNS servers

Configuration: [`os.dns_nameservers`](harvester-configuration.md#osdns_nameservers)

1. Edit the file

    ```
    /oem/99_custom.yaml
    ```

2. Search for this section and edit
    ```
    ```

3. Reboot the node.

## NTP servers


Configuration: [`os.ntp_servers`](harvester-configuration.md#osntp_servers)

### Runtime change

1. Edit the file

    ```
    /etc/systemd/timesyncd.conf
    ```

2. Replace `NTP=` setting with appropriate values:

    ```
    [Time]
    NTP = 0.suse.pool.ntp.org 1.suse.pool.ntp.org
    ```

3. Restart `systemd-timesyncd` service:

    ```
    systemctl restart systemd-timesyncd
    ```

### Persistent change
1. Edit the file

    ```
    /oem/99_custom.yaml
    ```

2. Search for this section and replace with appropriate values:
    ```
        timesyncd:
      NTP: 0.suse.pool.ntp.org 1.suse.pool.ntp.org
    ```

    (YAML path is `.stages.initramfs[0].timesyncd`).

3. Reboot the node.


## SSH Keys

## Passwords

