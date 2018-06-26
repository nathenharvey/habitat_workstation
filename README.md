# Habitat Workstation

Cookbooks and Packer config to create a workstation for use during Habitat workshops.

## Current Amazon Machine Image IDs

AMIs are currently only available in `us-east` region. Make sure you have a keypair setup in that region.
*NOTE*: Ensure your `~/.aws/config` region is set to `us-east-1` pending region abstraction.

An `~/.aws/config` file might look like:

```
[default]
region=us-east-1
aws_access_key_id = MYKEYID
aws_secret_access_key = MYACCESSKEY
```

Platform     | Hab 0.56.0            | none
----         | ------                | ----
CentOS 7     | ami-0f36a4015d35d109c | ami-06cb1643574bae9c8
Ubuntu 16.04 | ami-0216854b2a2ad08e6 | ami-074c27cb2c39554bb

## Pre-requisites

```
# The name of the AWS KeyPair to use for deployment
export $AWS_KEYPAIR_NAME='your_aws_keypair_name'
```

## Build the Amazon Machine Images (AMIs)

This rake task updates the vendored cookbooks before building the AMIs.

`$ rake cookbook:vendor`

### To create AMIs with Habitat installed

`$ rake "ami:build[ubuntu-1604,0.24.1]"`

The latest version of Habitat will be installed,
The version is for display purposes unless set to `none`

### To create AMIs without Habitat installed

`$ rake "ami:build[centos-7,none]"`

### List available templates

```
$ rake list:templates
centos-7
ubuntu-1604
```

## Share the AMIs with other Amazon accounts

```bash
$ export AMI_ID=the_ami_id_generated_by_packer
$ rake ami:share
```

## Deploy the CloudFormation stack

This deploys a CloudFormation stack with the number of hosts and TTL (days).
A template will be created in `stacks/` with the arguments above using values from
`config.yml`.  An example, `config.example.yml`, is included

```bash
# rake deploy[name,num_hosts,ttl]

$ export AMI_ID=the AMI_ID #(generated by the rake task "ami:build above)
$ rake "deploy[habihacks-stack,10,3]"
```

```
$ cat config.example.yml
---
contact: Human Chef
dept: Community Engineering
project: Habihacks
region: us-east-1
sg: sg-a1c3b1db
subnet: subnet-46b55431
type: t2.medium
```

## List Workstation IPs for a CloudFormation stack

```bash
$ rake "list:ips[habihacks-stack]"
workstation1: 54.172.141.159
workstation2: 54.87.195.76
workstation3: 54.91.122.177
workstation4: 54.86.46.187
workstation5: 54.227.190.60
```

## License

**Copyright:** 2017-2018, Chef Software, Inc.

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
