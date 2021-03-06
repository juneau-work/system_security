## 部署前 ##
1. 己安装好centos 7操作系统
2. 准备ansible环境

``` shell
# 安装pip
yum -y install python-setuptools
easy_install pip
# 安装ansible
pip install ansible
```

3. 配置主机列表**dev/hosts**
> - all表示所有主机
4. 定义变量**dev/group_vars/all**
> **注意:** 对于敏感数据，如远程用户名密码, 请事先通过以下脚本生成密文
``` shell
cat <<'EOF' > vault.sh
VAULT_ID='myVAULT@2018'
echo $VAULT_ID > ~/.vault_pass.txt

ANSIBLE_USER='root'
ANSIBLE_PASSWORD='root'

ansible-vault encrypt_string --vault-id ~/.vault_pass.txt $ANSIBLE_USER --name 'vault_ansible_user' | tee dev/group_vars/vault
ansible-vault encrypt_string --vault-id ~/.vault_pass.txt $ANSIBLE_PASSWORD --name 'vault_ansible_password' | tee -a dev/group_vars/vault
EOF

bash vault.sh
```





-------------------------------------------------------------------------------
#### 运行安全加固 ####
> ansible-playbook -i dev/hosts --vault-password-file ~/.vault_pass.txt run.yml --extra-vars install_or_uninstall=install





-------------------------------------------------------------------------------
#### 清除安全加固 ####
> ansible-playbook -i dev/hosts --vault-password-file ~/.vault_pass.txt run.yml --extra-vars install_or_uninstall=uninstall
  





-------------------------------------------------------------------------------
## 部署后 ##
**注意:** kdump可能需要重启后才生效，系统重启请谨慎操作
> ansible-playbook -i dev/hosts --vault-password-file ~/.vault_pass.txt reboot.yml
