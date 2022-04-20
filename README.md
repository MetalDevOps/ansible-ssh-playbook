### Automatização de concessão/revogação de acesso SSH

Esse playbook utiliza um inventario dinâmico, e a configuração do mesmo deve ser alterada conforme a necessidade.

### Para adicionar um novo usuário e concender acesso SSH

```
ansible-playbook -i aws_ec2.yaml --limit "tag_name_ninja" -e "action=concede" ssh.yml
```
```
[WARNING]: Found variable using reserved name: action

PLAY [servers] *****************************************************************************************************************************************************************************

TASK [ssh-access : Adciona novo usuárop] ***************************************************************************************************************************************************
The authenticity of host '44.202.192.253 (44.202.192.253)' can't be established.
ED25519 key fingerprint is SHA256:Hiw7CviGA5GuOYdb5mZjIZwkHK6CypUipqUIVcuQ06Y.
This host key is known by the following other names/addresses:
    ~/.ssh/known_hosts:3: ec2-44-202-192-253.compute-1.amazonaws.com
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
changed: [server2]
changed: [server1]

TASK [ssh-access : Concede acesso SSH ao usuário] ******************************************************************************************************************************************
changed: [server2]
changed: [server1]

TASK [ssh-access : Revoke user's SSH access] ***********************************************************************************************************************************************
skipping: [server1]
skipping: [server2]

TASK [ssh-access : Remove the user] ********************************************************************************************************************************************************
skipping: [server1]
skipping: [server2]

PLAY RECAP *********************************************************************************************************************************************************************************
server1                    : ok=2    changed=2    unreachable=0    failed=0    skipped=2    rescued=0    ignored=0
server2                    : ok=2    changed=2    unreachable=0    failed=0    skipped=2    rescued=0    ignored=0

```

### Para conceder acesso SSH a um usuário existente (pular a adição do usuário)
```
ansible-playbook -i aws_ec2.yaml --limit "tag_name_ninja" -e "action=concede" ssh.yml --skip-tags=add
```

### Para revogar o acesso SSH de um usuário existente (pular a remoção do usuário)
```
ansible-playbook -i aws_ec2.yaml --limit "tag_name_ninja" -e "action=revoga" ssh.yml --skip-tags=remove
```

### Para remover o usuário, e revogar o acesso SSH
```
ansible-playbook -i aws_ec2.yaml --limit "tag_name_ninja" -e "action=revoga" ssh.yml
```
```
[WARNING]: Found variable using reserved name: action

PLAY [servers] *****************************************************************************************************************************************************************************

TASK [ssh-access : Adciona novo usuário] ***************************************************************************************************************************************************
skipping: [server1]
skipping: [server2]

TASK [ssh-access : Concede acesso SSH ao usuário] ******************************************************************************************************************************************
skipping: [server1]
skipping: [server2]

TASK [ssh-access : Revoga acesso SSH do usuário] ***********************************************************************************************************************************************
changed: [server2]
changed: [server1]

TASK [ssh-access : Remove o usuário] ********************************************************************************************************************************************************
changed: [server1]
changed: [server2]

PLAY RECAP *********************************************************************************************************************************************************************************
server1                    : ok=2    changed=2    unreachable=0    failed=0    skipped=2    rescued=0    ignored=0
server2                    : ok=2    changed=2    unreachable=0    failed=0    skipped=2    rescued=0    ignored=0

```

### Para ver os resultados, faça login na conta de usuário que está configurado em "user_name" em um dos seus servidores
```
ssh dev@ec2-44-202-192-253.compute-1.amazonaws.com
Welcome to Ubuntu 20.04.3 LTS (GNU/Linux 5.11.0-1022-aws x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Wed Apr 13 19:44:06 UTC 2022

  System load:  0.0               Processes:             104
  Usage of /:   18.5% of 7.69GB   Users logged in:       0
  Memory usage: 21%               IPv4 address for eth0: 172.31.0.138
  Swap usage:   0%


1 update can be applied immediately.
To see these additional updates run: apt list --upgradable


The list of available updates is more than a week old.
To check for new updates run: sudo apt update


The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

dev@ip-172-31-0-138:~$
```

## Observações
1. Crie no diretório "keys" um diretório com o mesmo nome do "user_name" e coloque sua chave pública SSH nele.
2. Configure um usuário que possa ter acesso SSH sem senha (no meu caso - ninja) para os servidores de destino.
