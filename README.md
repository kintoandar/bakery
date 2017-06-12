Table of Contents
=================

  * [Bakery \- Baking delicious cloud instances](#bakery---baking-delicious-cloud-instances)
    * [Workflow](#workflow)
    * [Example](#example)
      * [Requirements](#requirements)
      * [How to](#how-to)
      * [Testing](#testing)
    * [Ready to be served](#ready-to-be-served)

# Bakery - Baking delicious cloud instances

This repository illustrates the proposed workflow described on [this blogpost](https://blog.kintoandar.com/2017/06/Baking-delicious-cloud-instances.html) for baking AMIs, launching instances and using Ansible to abstract both configuration logic flows, so I highly recommend you read it first.

## Workflow

The following sequence diagram describes the workflow:

![bakery_sequence](https://blog.kintoandar.com/images/bakery_sequence.jpg)

## Example

In this example, we want to spin up an instance with [Prometheus](https://prometheus.io/docs/introduction/overview/) using a separated data volume, which is useful for instance failures and service upgrades. The flow is something like this:

![bakery_flow](https://blog.kintoandar.com/images/bakery_flow.jpg)


### Requirements

This demo has the following requirements:

  * AWS API access tokens
  * Packer >= 1.0.0
  * Ansible >= 2.3.0.0

### How to

To generate an AMI to be used in this workflow you'll just need to run:

```bash
# Get the repo
git clone https://github.com/kintoandar/bakery.git
cd bakery

# Download ansible roles dependencies
ansible-galaxy install -r ./requirements.yml -p ./roles

# Use packer to build the AMI (using ansible)
export AWS_SECRET_ACCESS_KEY=XXXXXXXXXXXXXXXXXXXXXXXX
export AWS_ACCESS_KEY_ID=XXXXXXXXXXXXXXXXXX
packer build ./packer.json
```

### Testing

Now, to test the new AMI, just launch it with an extra EBS volume and use the provided [cloud-init configuration example](https://raw.githubusercontent.com/kintoandar/bakery/master/cloud-init-example.conf) as the [user-data](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/user-data.html). After the boot, you can check the cloud-init log in the instance:

```bash
less /var/log/cloud-init-output.log
```

Be sure that `/root/bakery/cloud-init.json` was created with all the required overrides for Ansible, specially the `cloud_init=true`.

## Ready to be served

Happy baking 🍰
