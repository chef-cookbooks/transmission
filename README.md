# transmission Cookbook

[![Build Status](https://travis-ci.org/chef-cookbooks/transmission.svg?branch=master)](http://travis-ci.org/chef-cookbooks/transmission) [![Cookbook Version](https://img.shields.io/cookbook/v/transmission.svg)](https://supermarket.chef.io/cookbooks/transmission)

Installs the [Transmission BitTorrent Client](http://www.transmissionbt.com) and includes a `transmission_torrent_file` LWRP.

## Requirements

### Platforms

- Debian/Ubuntu
- RHEL/CentOS/Scientific/Amazon/Oracle 7+
- Fedora

Note: Package installs are only available on Ubuntu/Debian. Source installs are possible on all platforms

### Chef

- Chef 12.1+

### Cookbooks

- build-essential - for compiling the source.
- openssl - for generating a secure password.

## Attributes

- `node["transmission"]["peer_port"]` - The port Transmission listens on for peering, default `51413`.
- `node["transmission"]["rpc_bind_address"]` - Where to listen for RPC connections, default `0.0.0.0`.
- `node["transmission"]["rpc_port"]` - The port Transmission listens on for remote services, default `9091`.
- `node["transmission"]["rpc_username"]` - The username required to access remote services, default `transmission`.
- `node["transmission"]["rpc_password"]` - The sha1 encrypted password, default generated by `secure_password`.
- `node["transmission"]["home"]` - Transmission's home directory, default `/var/lib/transmission-daemon`.
- `node["transmission"]["config_dir"]` - Transmission's config directory, default `/var/lib/transmission-daemon/info`.
- `node["transmission"]["download_dir"]` - Directory to move complete files to, default `/var/lib/transmission-daemon/downloads`.
- `node["transmission"]["incomplete_dir"]` - Directory to keep incomplete files in, default `/var/lib/transmission-daemon/incomplete`.
- `node["transmission"]["incomplete_dir_enabled"]` - Whether the incomplete file directory is used, default `false`.
- `node["transmission"]["speed_limit_down"]` - The KB/s limit for downloading files, default `100`.
- `node["transmission"]["speed_limit_down_enabled"]` - Whether the download limit is used, default `false`.
- `node["transmission"]["speed_limit_up"]` - The KB/s limit for uploading files, default `100`.
- `node["transmission"]["speed_limit_up_enabled"]` - Whether the upload limit is used, default `false`.
- `node["transmission"]["watch_dir"]` - Directory to watch for new torrent files, default `/var/lib/transmission-daemon/watch`.
- `node["transmission"]["watch_dir_enabled"]` - Whether the watch file directory is used, default `false`.

The file also contains the following attribute types:

- platform specific locations and settings.
- source installation settings

## Resource/Provider

### `transmission_torrent_file`

Download a file via the [BitTorrent protocol](http://en.wikipedia.org/wiki/BitTorrent). The usage semantics are like that of the existing [file](http://wiki.chef.io/display/chef/Resources#Resources-File) and [remote_file](http://wiki.chef.io/display/chef/Resources#Resources-RemoteFile) resources. This allows very fast downloads for files that are part of large BitTorrent swarms. The Ubuntu 10.04 LTS ISO (around 700MB) downloads in about 50 seconds.

#### Actions

- :create: Download a file via the BitTorrent protocol

#### Attribute Parameters

- path: name attribute. the path to the file
- torrent: torrent file of the swarm to join. can either be a url or local file path
- blocking: should the file be downloaded in a blocking way? If `true` Chef will download the file in a single Chef run, if `false` Chef will check for a completed download during each Chef run until the download is complete. default is `true`.
- continue_seeding: should the file continue to be seeded to the swarm after download? default is `false`.
- owner: The owner for the file
- group: The group owner of the file (string or id)
- rpc_host: the address of the Transmission RPC host to connect to. default is `localhost`.
- rpc_port: the port of the Transmission RPC host to connect to. default is `9091`.
- rpc_username: the username of the Transmission RPC account. default is `transmission`.
- rpc_password: the password of the Transmission RPC account . should probably be `node['transmission']['rpc_password']` which by default is a secure password generated for this node.

#### Examples

Download the lucid ISO

```ruby
transmission_torrent_file "/home/ubuntu/ubuntu.iso"  do
  torrent "http://releases.ubuntu.com/lucid/ubuntu-10.04.1-server-i386.iso.torrent"
  owner 'ubuntu'
  group 'ubuntu'
  rpc_username node['transmission']['rpc_username']
  rpc_password node['transmission']['rpc_password']
  action :create
end
```

Continue seeding after download

```ruby
transmission_torrent_file "/home/ubuntu/ubuntu.iso"  do
  torrent "http://releases.ubuntu.com/lucid/ubuntu-10.04.1-server-i386.iso.torrent"
  owner 'ubuntu'
  group 'ubuntu'
  continue_seeding true
  rpc_username node['transmission']['rpc_username']
  rpc_password node['transmission']['rpc_password']
  action :create
end
```

## Usage

### default

Include default recipe in a run list, to get some Transmission installed. Installs Transmission by package or source depending on the platform.

### package

Installs Transmission from packages. This should only be loaded by the default recipe.

### source

Installs Transmission from source. This should only be loaded by the default recipe.

## License & Authors

**Author:** Cookbook Engineering Team ([cookbooks@chef.io](mailto:cookbooks@chef.io))

**Copyright:** 2011-2016, Chef Software, Inc.

```
Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```
