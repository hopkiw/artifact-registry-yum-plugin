## YUM and DNF plugins for Google Artifact Registry

This repository contains YUM and DNF plugins for accessing Google Artifact
Registry package repositories requiring authentication.

**Table of Contents**

* [Overview](#overview)
* [Usage](#usage)
    * [Installing](#installing)
    * [Configuring](#configuring)

## Overview

YUM and DNF package repositories are typically either publicly readable, or
privately available within an organization's internal networks. Google Artifact
Registry repositories however support *authenticated access* repositories,
limiting access to specific user or service account entities defined within
Google Cloud Platform IAM. To support this use case, we offer these YUM and DNF
plugins, which transparently authenticate as needed during YUM or DNF
transactions.

## Usage

#### Installing

RPM packages containing these plugins are available at TODO. Installation should
be as simple as:
   ```
   # yum install -y yum-plugin-artifact-registry
   ```
or
   ```
   # dnf install -y dnf-plugin-artifact-registry
   ```
TODO: do we need repo setup instructions? Should we reference the http path
directly?

#### Configuring

The plugin will work out of the box as soon as it is installed. Any
repository definition using a `baseurl` option containing the string 'pkg.dev'
will have authentication headers added automatically. The default behavior for
finding credentials is similar to other Google Cloud auth tools, first looking
for the GOOGLE\_APPLICATION\_CREDENTIALS environment variable, then trying well
known paths, then trying to autodetect whether the system is on GCE or GAE and
using the default service account. For more information on this behavior see
TODO.

Additionally, as setting environment variables may be difficult for system
utilities such as YUM/DNF (for example during automatic package updates), we
offer a config file option for these plugins. It lives at
`/etc/dnf/plugins/artifact-registry.conf` for DNF and
`/etc/yum/pluginconf.d/artifact-registry.conf` for YUM, and supports the
following options:
   ```
   [main]
   service_account_json = /path/to/creds.json
   ```

Where `service_account_json` is a fully qualified path to a JSON formatted file
containing service account credentials, as you would produce using `gcloud
command here` or exporting a service account credential from the GCP console.
Use it as you would the GOOGLE\_APPLICATION\_CREDENTIALS environment variable.
For more information, see TODO.
   ```
   [main]
   service_account_email = email-address@domain.tld
   ```
Where `service_account_email` is the email address of a GCP service account. This
option allows GCE VMs (TODO or GAE?) to specify a service account which is
associated with the VM, rather than choosing the 'default' service account. This
is useful when your VM has multiple service accounts associated.

If you want to use the default service account on GCE or GAE, do not specify any
config options. It is expected that you will only set one of
`service_account_json` and `service_account_email`; if you specify both,
`service_account_json` will take precedence.
