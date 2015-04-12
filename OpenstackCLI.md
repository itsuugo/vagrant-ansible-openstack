# Tips for openstack usage from CLI

source scripts/admin.rc

##Launch image from CLI

neutron net-list

nova flavor-list
nova image-list
nova list
nova show "id"

nova boot --flavor 1 --image "imageid" --nic net-id="" VM_NAME
