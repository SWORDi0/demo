#_______________________________________BR-SRV__________________________________________

cp /mnt/playbook/get_hostname_address.yml /etc/ansible/playbook.yml
chmod 777 /etc/ansible/playbook.yml

vim /etc/ansible/playbook.yml

- name: PC-INFO
  hosts: LMX
  tasks:
    - name: получение данных с хоста
      copy:
        dest: /etc/ansible/PC-INFO/{{ ansible_hostname }}.txt
        content: |
          Hostname: {{ ansible_hostname }}
          IP_Address: {{ ansible_default_ipv4.address }}
      delegate_to: 127.0.0.1

vim /etc/ansible/hosts

hq-rtr ansible_ssh_host=172.16.1.2 ansible_ssh_user=net_admin ansible_ssh_pass=P@ssw0rd
br-rtr ansible_ssh_host=192.16.3.1 ansible_ssh_user=net_admin ansible_ssh_pass=P@ssw0rd
[LNX]
hq-cli ansible_ssh_host=192.16.200.3 ansible_ssh_user=Administrator ansible_ssh_pass=P@ssw0rd
hq-srv ansible_ssh_host=192.16.100.2 ansible_ssh_user=sshuser ansible_ssh_pass=P@ssw0rd ansible_ssh_port=2026


ansible-playbook /etc/ansible/playbook.yml
mkdir -p /etc/ansible/PC-INFO
