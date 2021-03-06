# freebsd Cookbook

[![Build Status](https://travis-ci.org/chef-cookbooks/freebsd.svg?branch=master)](http://travis-ci.org/chef-cookbooks/freebsd) [![Cookbook Version](https://img.shields.io/cookbook/v/freebsd.svg)](https://supermarket.chef.io/cookbooks/freebsd)

Sets up ports and pkgng on FreeBSD systems

## Requirements

### Platforms

- FreeBSD 9+

### Chef

- Chef 12.1+

### Cookbooks

- none

## Attributes

Attribute                                 | Default | Description
----------------------------------------- | ------- | ------------------------------------------
`node['freebsd']['compiletime_portsnap']` | `false` | Execute portsnap resources at compile time

## Supported Versions

This cookbook will support stable and release [versions](https://www.freebsd.org/security/index.html#sup) of the FreeBSD Platform. More information on this subject can be found at [issue23](https://github.com/chef-cookbooks/freebsd/issues/23).

## Usage

### freebsd::pkgng

This recipe ensures `pkg` (aka `pkgng`), FreeBSD's next generation package management tool, is installed and configured.

This recipe is only useful on FreeBSD versions before 10 as `pkg` ships in the base install of FreeBSD 10+. That being said the recipe is safe to include on the runlists of FreeBSD 10 nodes and will mostly operate in a no-op mode.

### freebsd::portsnap

This recipe ensures the Ports Collection collection is fully up to date.

This recipe should appear first in the run list of FreeBSD nodes to ensure that the package cache is up to date before managing any `package` resources with Chef.

## Resources/Providers

### port
Installs and uninstalls FreeBSD ports

#### Actions

Action  | Description    | Default
------- | ---------------| -------
install | install port   | Yes
remove  | uninstall port | No

#### Attributes

Attribute | Description
--------- | ---------------------------------------------------------------------------------------------------------------
name      | the name of the port
options   | if the attribute is set, options will be written to /var/db/ports before build


### port_options

Provides an easy way to set port options from within a cookbook.

It can be used in two different ways:

- template-based: specifying a source will write it to the correct destination with no change;
- options hash: if a options hash is passed instead, it will be merged on top of default and current options, and the result will be written back.

Options hash take simple option names as keys and boolean as values; when saving to file, this is converted to the format that FreeBSD ports expect:

Option Key Name | Option Value | Options File
--------------- | ------------ | -------------------
MAILHEAD        | true         | OPTIONS_FILE_SET+=MAILHEAD
MAILHEAD        | false        | OPTIONS_FILE_UNSET+=MAILHEAD

IMPORTANT!!! Only boolean values are supported now, for packages with radio options (like sqlite3) please use the template-based way.

#### Actions

Action | Description                                                 | Default
------ | ----------------------------------------------------------- | -------
create | create the port options file according to the given options | Yes

#### Attributes

Attribute | Description
--------- | ---------------------------------------------------------------------------------------------------------------
name      | the name of the port whose options file you want to manipulate
source    | if the attribute is set, it will be used to look up a template, which will then be saved as a port options file
options   | a hash with the option name as the key, and a boolean as value

#### Examples

```ruby
# Install "mc" from ports with current (if present) or default options
freebsd_port "mc"

# Set options for "php56" and install the port
freebsd_port "php56" do
  options mailhead: true, ipv6: false
end

# freebsd-php5-options.erb will be written out as /var/db/ports/lang_php56/options
# Use this if you need to generate options file manually
freebsd_port_options "php56" do
  source "freebsd-php5-options.erb"
end

# Default and current options will be taken from 'make -V VAR_NAME' commands;
# the MAILHEAD option will be set to true, IPV6 to false and the others will be unchanged
freebsd_port_options "php56" do
  options mailhead: true, ipv6: false
end
```

## License & Authors

- Author: Andrea Campi ([andrea.campi@zephirworks.com](mailto:andrea.campi@zephirworks.com))
- Author: Seth Chisamore ([schisamo@chef.io](mailto:schisamo@chef.io))

```text
Copyright 2010-2012, ZephirWorks
Copyright 2012-2016, Chef Software, Inc. (<legal@chef.io>)

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
