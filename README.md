############################################################

Playbooks for automating the deployment and deletion of instances on google cloud platform
including extra disks and external ips per instance

NOTE: GCP Functionality Requires on machine used to run the playbooks:

ansible >=2.6
apache-libcloud==0.17.0
google-auth

For GCP cloud:

Sample inventory file can be found in inventories/inventories_gcp

Deploy instances:  GCPCreateInstance.yml  
- creates bootdisk and extra disks
- sets up external ip address
- creates instances

Destroy environmnet: GCPDeleteInstance.yml
- Deletes instances
- Deletes disks
- Deletes external address


Update the vars/gcp_vars.yml file with the information for your project in GCP



############################################################

Openshift on prem deployment playbooks once machines are up:

Below is the procedure for running the disconnected install with the playbooks in this repo.  
Sample inventory file is under inventories/inventory_os


Prerequisites:
  Collect the images in tar files, check that the vars/imagelist_vars.yml is up to date for the images you collected
  Setup yum repo with the following repos (under "repos" directory):

  rhel-7-server-rpms

  rhel-7-server-extras-rpms

  rhel-7-server-ansible-2.6-rpms
		
  rhel-7-server-ose-3.11-rpms

1. Login to the deployer and set the yum repo to the internal repo ( you can copy setup_internal_repos.yml from include directory and manually edit IP)
2. Install ansible
3. Insure ssh connection to all instances without login by sharing ssh keys
4. Copy container image tars to deployer location you specified in inventory_os file
5. Run SetupDeployer.yml
6. Run pre-install.yml
7. Install openshfit-ansible on deployer:

   sudo yum install openshift-ansible -y

8. run the installation playbooks:

  cd /usr/share/ansible/openshift-ansible
  
  ansible-playbook -i inventory  playbooks/prerequisites.yml
  
  ansible-playbook -i inventory playbooks/deploy_cluster.yml



############################################################

Playbooks Details:

SetupDeployer.yml:
- Installs docker
- Install docker Registry
- Loads, Re-Tags and Pushes Images to Registry


pre-install.yml:
- Sets up yum repo configuration to point to internal repo
- Sets ntp
- Updates hosts file
- Installs docker (including storage on separate disk)
- Sets up etcd storage on separate disk (optional)
- Install support packages
