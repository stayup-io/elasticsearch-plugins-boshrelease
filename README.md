# BOSH Release for elasticsearch-plugins

Provides package with HQ and kopf es plugins. This can later be integrated on any elasticsearch bosh deployment.

## Integration with logsearch

Upload release

```
git clone https://github.com/cloudfoundry-community/elasticsearch-plugins-boshrelease.git
cd elasticsearch-plugins-boshrelease
bosh upload release releases/elasticsearch-plugins-1.yml
```

On your logsearch deployment collocate this release:

```
...
releases:
- name: logsearch
  version: latest
- name: elasticsearch-plugins
  version: latest
...
jobs:
...
- name: api
  releases:
  - name: logsearch
  templates:
  - name: elasticsearch-plugins
    release: elasticsearch-plugins
  - name: elasticsearch
    release: logsearch
  - name: api
    release: logsearch
  - name: kibana
    release: logsearch
  update:
    serial: true
  instances: 1
  resource_pool: small_z1
  networks:
  - name: microbosh-network
    static_ips:
    - 10.0.23.11
  properties:
    elasticsearch:
      plugins:
      - hq: file:///var/vcap/packages/elasticsearch-plugins/elasticsearch-hq.zip
      - kopf: file:///var/vcap/packages/elasticsearch-plugins/elasticsearch-kopf.zip
      node:
        allow_data: false
```

### Development

As a developer of this release, create new releases and upload them:

```
bosh create release --force && bosh -n upload release
```

### Final releases

To share final releases:

```
bosh create release --final
```

By default the version number will be bumped to the next major number. You can specify alternate versions:


```
bosh create release --final --version 2.1
```
