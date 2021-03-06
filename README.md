# Introduction

This module is an implementation of Docker support for the OpenStack
puppet-designate module.

For example, in our environment we use the following block to add Docker
support to the Designate module:

    class { '::os_docker::designate':
      release_name => 'kilo',
    }
    include ::os_docker::designate::api"
    include ::os_docker::designate::central"
    include ::os_docker::designate::sink"

For docker support, you should include the corresponding class for each service
you wish to configure as shown above.

## Supported Projects

 * Designate
 * Heat

## Configuration
For docker support, you will want to disable packaging installation.  You can
do this for Designate by adding the following to your Hiera config:

    designate::package_ensure: absent
    designate::api::package_ensure: absent
    designate::central::package_ensure: absent
    designate::sink::package_ensure: absent

The os_docker module contains example configuration files for the Juno, Kilo
and Liberty versions of each supported project.  These configuration files can
be overridden by passing in a new config_files parameter to either class.  Note
that the main config file for each project (`designate.conf`, `heat.conf`,
etc.) is only used if the file does not already exist and will be copied in
place before any `*_config` resources are applied.  On subsequent runs
the main config file will not be replaced unless the release_name parameter is
changed.  If you do not pass in a config_files parameter then example config
files from the release you specify will be used.

The service specific classes (os_docker::<project>::<service>) will create init
script wrappers using the puppet-docker module, but turn off service management
via puppet-docker.  This allows the existing puppet module's service
configuration and dependency trees to function the same as when using packages.

This example shows a Juno designate docker image being configured to be pulled
locally and activated via Hiera.

    os_docker::designate::release_name: juno
    os_docker::designate::active_image_name: blobmaster:5000/cirrus/designate
    os_docker::designate::active_image_tag: 2014.2-13-g99db2f6.11.2027fed
