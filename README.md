# Overview

This interface layer handles the communication with Etcd via the `etcd-proxy` interface.

# Usage

## Requires

This interface layer will set the following states, as appropriate:

  * `{relation_name}.connected` The relation is established, but Etcd may not
  yet have provided any connection or service information.

  * `{relation_name}.available` Etcd has provided its cluster string
    information, and is ready to handle incoming connections.
    The provided information can be accessed via the following methods:
      * `cluster_string()`


For example, a common application for this is configuring an applications
backend kv storage, like Docker.

```python
@when('proxy.available')
def prepare_etcd_proxy(proxy):
    con_string = proxy.cluster_string()
    opts = {}
    opts['cluster_string'] = con_string
    render('proxy_systemd_template', '/etc/systemd/system/etcd-proxy.service', opts)

```


## Provides

A charm providing this interface is providing the Etcd cluster management
connection string. This is similar to what ETCD requires when peering, declared as:

```shell
etcd0=https://192.168.1.2:2380,etcd1=https://192.168.2.22:2380
```

This interface layer will set the following states, as appropriate:

  * `{relation_name}.connected` One or more clients of any type have
    been related.  The charm should call the following methods to provide
    the appropriate information to the clients:

    * `{relation_name}.provide_cluster_string()`

#### Example (with the assumption you have layer-etcd included):

```python
@when('proxy.connected')
def send_cluster_details(proxy):
    etcd = EtcdHelper()
    proxy.provide_cluster_string(etcd.cluster_string())
```


# Contact Information

### Maintainer
- Charles Butler <charles.butler@canonical.com>


# Etcd

- [Etcd](https://coreos.com/etcd/) home page
- [Etcd bug trackers](https://github.com/coreos/etcd/issues)
- [Etcd Juju Charm](http://jujucharms.com/?text=etcd)
