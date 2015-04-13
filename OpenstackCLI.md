# Tips for openstack usage from CLI

source scripts/openstack-admin-example.rc

## Networking

# neutron net-create --tenant-id DEMO_TENANT_ID demo-net SPECIAL_OPTIONS
# neutron subnet-create --tenant-id DEMO_TENANT_ID demo-net 10.5.5.0/24 --gateway 10.5.5.1
# neutron router-interface-add EXT_TO_INT_ID DEMO_NET_SUBNET_ID

##Launch image from CLI

neutron net-list

nova flavor-list
nova image-list
nova list
nova show "id"

nova boot --flavor 1 --image "imageid" --nic net-id="" VM_NAME


