# Ansible

### Installing Ansible

This project requires ansible 2.9.24 or greater on the host running the ansible playbook. The target systems do no not need Ansible installed.

The steps below for installing Ansible have been tested on CentOS 7.9.2009, CentOS 8.4.2105, Debian 9.13, Debian 10.10, Ubuntu 18.04.5, and Ubuntu 20.04.3.

1. Ensure pip3 is installed 
    
    Ubuntu/Debian: `sudo apt install python3-pip`
    
    CentOS: `sudo yum install python3-pip`

2. Add local bin directory to path in bashrc
    
    ```
    echo 'PATH=$PATH:$HOME/.local/bin' >> ~/.bashrc
    source ~/.bashrc
    ```

3. Use pip to install ansible

    `python3 -m pip install --user -U pip && python3 -m pip install --user -U ansible`

4. Ensure that ansible version is greater than 2.9.24

    `ansible --version`

## Kasm Multi Server install
This playbook will deploy Kasm Workspaces in a multi-server deployment using Ansible. 

* It installs the kasm components on the systems specified in the ansible `inventory` required for the respective roles (db, web, agent).
* It creates a new swapfile to ensure that the total swap space matches the size `desired_swap_size` specified on the files in group_vars/.
* It enables the docker daemon to run at boot to ensure that kasm services are started after a reboot.

It has been tested on CentOS 7.9.2009, CentOS 8.4.2105, Debian 9.13, Debian 10.10, Ubuntu 18.04.5, and Ubuntu 20.04.3

![Diagram][Image_Diagram]

[Image_Diagram]: https://f.hubspotusercontent30.net/hubfs/5856039/Ansible/Ansible%20Multi%20Server.png "Diagram"


### Ansible Configuration

1. Open `roles/install_common/vars/main.yml`, `group_vars/agent.yml` and update variables if desired.

2. Open `inventory` file and fill in the hostnames / ips for the servers that will be fulfilling the agent, webapp and db roles. 
   
3. Run the deployment.

    `ansible-playbook -Kk -u [username] -i inventory install_kasm.yml`

    Ansible will prompt you for the ssh password and sudo password (will almost always be the same password).

    Or, if you have ssh keys copied over to your servers and have NOPASSWD in sudoers you can just run.

    `ansible-playbook -u [username] -i inventory install_kasm.yml`

4. Login to the deployment as admin@kasm.local using the IP of one of the WebApp servers (eg https://192.168.1.2)

5. Navigate to the Agents tab, and enable each Agent after it checks in. (May take a few minutes)

## Kasm Uninstall playbook

This playbook uninstalls Kasm workspaces from DB, WebApp and Agent servers specified in the `inventory` file.

It has been tested on CentOS 7.9.2009, CentOS 8.4.2105, Debian 9.13, Debian 10.10, Ubuntu 18.04.5, and Ubuntu 20.04.3

### Ansible Configuration

1. Open `inventory` file and fill in the hostnames / ips for the servers that will be fulfilling the agent, webapp and db roles. 

3. Run the deployment.

    `ansible-playbook -Kk -u [username] -i inventory uninstall_kasm.yml`

    Ansible will prompt you for the ssh password and sudo password (will almost always be the same password).

    Or, if you have ssh keys copied over to your servers and have NOPASSWD in sudoers you can just run.

    `ansible-playbook -u [username] -i inventory uninstall_kasm.yml`

## Kasm Stop/Start/Restart playbooks

These playbooks can be used to start, stop or restart Kasm workspaces services on the DB, WebApp and Agent servers specified in the `inventory` file.

It can be limited to run only on hosts in specific groups by passing `-l [db, web, or agent]` flag.

In the examples `restart_kasm.yml` can be substituted for `start_kasm.yml` or `stop_kasm.yml` for starting or stopping the kasm services respectively.

### Ansible Configuration

1. Open `inventory` file and fill in the hostnames / ips for the servers that will be fulfilling the agent, webapp and db roles. 

2. Run the playbook.

    `ansible-playbook -Kk -u [username] -i inventory restart_kasm.yml`

    Ansible will prompt you for the ssh password and sudo password (will almost always be the same password).

    Or, if you have ssh keys copied over to your servers and have NOPASSWD in sudoers you can just run.

    `ansible-playbook -u [username] -i inventory restart_kasm.yml`

    If you only want to run it against hosts in the 'db' group for example you can run the following:

    `ansible-playbook -u [username] -l db -i inventory restart_kasm.yml`

## Kasm Database Backup playbook

This playbook can be used to backup the Kasm Workspaces database to a location on the Database server specified by `remote_backup_dir` and optionally to a location on the ansible server specified by `local_backup_dir`. Backups older than `retention_days` are automatically cleaned up.

### Ansible Configuration

1. Open `roles/backup_db/vars/main.yml` and update variables if desired.

2. Open `inventory` file and fill in the hostnames / ips for the servers that will be fulfilling the agent, webapp and db roles. 

3. Run the playbook.

    `ansible-playbook -Kk -u [username] -i inventory backup_db.yml`

    Ansible will prompt you for the ssh password and sudo password (will almost always be the same password).

    Or, if you have ssh keys copied over to your servers and have NOPASSWD in sudoers you can just run.

    `ansible-playbook -u [username] -i inventory backup_db.yml`

## OS Patching Playbook

This playbook is used for patching the underlying OSes on the Kasm Workspace servers. It will patch and reboot the servers if needed.

### Ansible Configuration

1. Open `roles/patch_os/vars/main.yml` and update variables if desired.

2. Open `inventory` file and fill in the hostnames / ips for the servers that will be fulfilling the agent, webapp and db roles. 

3. Run the playbook.

    `ansible-playbook -Kk -u [username] -i inventory patch_os.yml`

    Ansible will prompt you for the ssh password and sudo password (will almost always be the same password).

    Or, if you have ssh keys copied over to your servers and have NOPASSWD in sudoers you can just run.

    `ansible-playbook -u [username] -i inventory patch_os.yml`
