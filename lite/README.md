# Lite-VM Concourse deployment

A "lite" Concourse deployment will co-locate everything together, without a
BOSH director. The only real advantage of this is that it's a single VM, making
it easy to get your feet wet, but it won't help you scale later when you need
it.

Note that without a BOSH director, you really won't be able to use any BOSH
commands except `create-env` to create the VM and `delete-env` to delete it.
There is no `bosh ssh`, `bosh restart`, etc., and if something goes wrong, your
only real recourse is to just `delete-env` and try again.



Prereqs - 
> BOSH director already setup using BBL
> VPC CIDR = 10.0.0.0/16
> Make sure you've stemcell uploaded to the BOSH
> In the Security Group associated with BOSH director add Inbound rule to allow all traffic from CIDR - 10.0.0.0/16
> Elastic/public IP created 
> DNS entry for cicd.<your domain.com> added to Hosted Zone and is mapped to the Elastic IP
> Make sure IG is configured
> Make sure IG is mapped in the Route Table used in the subnet


Run following command - 
bosh deploy -d concourse concourse-bosh-deploy.yml \
 -l ../versions.yml \
 --vars-store cluster-creds.yml \
 -o operations/no-auth.yml \
 -o operations/basic-auth.yml \
 -o operations/vip-network.yml \
 --var external_url=http://cicd.<your domain.com>:8080 \
 --var network_name=default \
 --var concourse_vm_type=large \
 --var db_persistent_disk_type=default \
 --var deployment_name=concourse \
 --var atc_basic_auth.username=<username> \
 --var atc_basic_auth.password=<password> \
 --var vip_ip=<Elastic IP mapped to cicd.yourdomain> \
 --var concourse_release_url=https://s3.amazonaws.com/bosh-worshop-copiled-releases/3468.5/concourse-3.13.0-ubuntu-trusty-3468.5.tgz \
 --var garden_runc_release_url=https://s3.amazonaws.com/bosh-worshop-copiled-releases/3468.5/garden-runc-1.13.1-ubuntu-trusty-3468.5.tgz \
 --var postgres_release_url=https://s3.amazonaws.com/bosh-worshop-copiled-releases/3468.5/postgres-28-ubuntu-trusty-3468.5.tgz

