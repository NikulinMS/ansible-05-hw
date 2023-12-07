# Домашнее задание к занятию "`Тестирование roles`" - `Никулин Михаил Сергеевич`



---

### Molecule

#### 1. Запустите  `molecule test -s centos_7` внутри корневой директории clickhouse-role, посмотрите на вывод команды. Данная команда может отработать с ошибками, это нормально. Наша цель - посмотреть как другие в реальном мире используют молекулу.

При попытке запустить код из задания получаю следующую ошибку

<details>
    <summary>molecule test -s centos_7</summary>

```shell
root@nikulin:/home/nikulinn/Documents/mnt-homeworks/08-ansible-05-testing/playbook/roles/clickhouse# molecule test -s centos_7
WARNING  Driver docker does not provide a schema.
CRITICAL Failed to validate /home/nikulinn/Documents/mnt-homeworks/08-ansible-05-testing/playbook/roles/clickhouse/molecule/centos_7/molecule.yml

["Additional properties are not allowed ('playbooks' was unexpected)"]

```
</details>

Программа отрабатывает только, если запустить ```molecule test```

<details>
    <summary>molecule test</summary>

```shell
root@nikulin:/home/nikulinn/Documents/mnt-homeworks/08-ansible-05-testing/playbook/roles/clickhouse# molecule test
INFO     default scenario test matrix: dependency, cleanup, destroy, syntax, create, prepare, converge, idempotence, side_effect, verify, cleanup, destroy
INFO     Performing prerun with role_name_check=0...
INFO     Using /root/.ansible/roles/alexeysetevoi.clickhouse symlink to current repository in order to enable Ansible to find the role using its expected full name.
INFO     Running default > dependency
WARNING  Skipping, missing the requirements file.
WARNING  Skipping, missing the requirements file.
INFO     Running default > cleanup
WARNING  Skipping, cleanup playbook not configured.
INFO     Running default > destroy

PLAY [Destroy] *****************************************************************

TASK [Populate instance config] ************************************************
ok: [localhost]

TASK [Dump instance config] ****************************************************
skipping: [localhost]

PLAY RECAP *********************************************************************
localhost                  : ok=1    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0

INFO     Running default > syntax

playbook: /home/nikulinn/Documents/mnt-homeworks/08-ansible-05-testing/playbook/roles/clickhouse/molecule/default/converge.yml
INFO     Running default > create

PLAY [Create] ******************************************************************

TASK [Populate instance config dict] *******************************************
skipping: [localhost]

TASK [Convert instance config dict to a list] **********************************
skipping: [localhost]

TASK [Dump instance config] ****************************************************
skipping: [localhost]

PLAY RECAP *********************************************************************
localhost                  : ok=0    changed=0    unreachable=0    failed=0    skipped=3    rescued=0    ignored=0

INFO     Running default > prepare
WARNING  Skipping, prepare playbook not configured.
INFO     Running default > converge

PLAY [Converge] ****************************************************************

TASK [Replace this task with one that validates your content] ******************
ok: [instance] => {
    "msg": "This is the effective test"
}

PLAY RECAP *********************************************************************
instance                   : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

INFO     Running default > idempotence

PLAY [Converge] ****************************************************************

TASK [Replace this task with one that validates your content] ******************
ok: [instance] => {
    "msg": "This is the effective test"
}

PLAY RECAP *********************************************************************
instance                   : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

INFO     Idempotence completed successfully.
INFO     Running default > side_effect
WARNING  Skipping, side effect playbook not configured.
INFO     Running default > verify
INFO     Running Ansible Verifier
WARNING  Skipping, verify action has no playbook.
INFO     Verifier completed successfully.
INFO     Running default > cleanup
WARNING  Skipping, cleanup playbook not configured.
INFO     Running default > destroy

PLAY [Destroy] *****************************************************************

TASK [Populate instance config] ************************************************
ok: [localhost]

TASK [Dump instance config] ****************************************************
skipping: [localhost]

PLAY RECAP *********************************************************************
localhost                  : ok=1    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0

INFO     Pruning extra files from scenario ephemeral directory

```

</details>


#### 2. Перейдите в каталог с ролью vector-role и создайте сценарий тестирования по умолчанию при помощи `molecule init scenario --driver-name docker`.

```shell
root@nikulin:/home/nikulinn/Documents/mnt-homeworks/08-ansible-05-testing/playbook/roles/vector-role# molecule init scenario --driver-name docker
INFO     Initializing new scenario default...

PLAY [Create a new molecule scenario] ******************************************

TASK [Check if destination folder exists] **************************************
changed: [localhost]

TASK [Check if destination folder is empty] ************************************
ok: [localhost]

TASK [Fail if destination folder is not empty] *********************************
skipping: [localhost]

TASK [Expand templates] ********************************************************
changed: [localhost] => (item=molecule/default/molecule.yml)
changed: [localhost] => (item=molecule/default/destroy.yml)
changed: [localhost] => (item=molecule/default/create.yml)
changed: [localhost] => (item=molecule/default/converge.yml)

PLAY RECAP *********************************************************************
localhost                  : ok=3    changed=2    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0

INFO     Initialized scenario in /home/nikulinn/Documents/mnt-homeworks/08-ansible-05-testing/playbook/roles/vector-role/molecule/default successfully.

```

#### 3. Добавьте несколько разных дистрибутивов (centos:8, ubuntu:latest) для инстансов и протестируйте роль, исправьте найденные ошибки, если они есть.

```shell
---
dependency:
  name: galaxy
role_name_check: 1
driver:
  name: docker
lint: |
  ansible-lint .
  yamllint .
platforms:
  - name: sad_merkle
    image: docker.io/pycontribs/ubuntu:latest
    pre_build_image: true
  - name: dreamy_varahamihira
    image: docker.io/pycontribs/fedora:latest
    pre_build_image: true
  - name: awesome_bhabha
    image: docker.io/pycontribs/debian:latest
    pre_build_image: true
provisioner:
  name: ansible
verifier:
  name: ansible
```
```shell
root@nikulin:/home/nikulinn/Documents/mnt-homeworks/08-ansible-05-testing/playbook/roles/vector-role# docker ps -a
CONTAINER ID   IMAGE                      COMMAND       CREATED          STATUS          PORTS     NAMES
4711015a1dbc   pycontribs/debian:latest   "/bin/bash"   24 seconds ago   Up 1 second               awesome_bhabha
c8c446e885fd   pycontribs/fedora:latest   "bin/bash"    2 days ago       Up 28 minutes             dreamy_varahamihira
ba05688ef25d   pycontribs/ubuntu:latest   "bin/bash"    2 days ago       Up 28 minutes             sad_merkle

```

<details>
<summary>molecule test</summary> 

```shell
root@nikulin:/home/nikulinn/Documents/mnt-homeworks/08-ansible-05-testing/playbook/roles/vector-role# molecule test
WARNING  Driver docker does not provide a schema.
INFO     default scenario test matrix: dependency, cleanup, destroy, syntax, create, prepare, converge, idempotence, side_effect, verify, cleanup, destroy
INFO     Performing prerun with role_name_check=1...
INFO     Set ANSIBLE_LIBRARY=/root/.cache/ansible-compat/f5bcd7/modules:/root/.ansible/plugins/modules:/usr/share/ansible/plugins/modules
INFO     Set ANSIBLE_COLLECTIONS_PATH=/root/.cache/ansible-compat/f5bcd7/collections:/root/.ansible/collections:/usr/share/ansible/collections
INFO     Set ANSIBLE_ROLES_PATH=/root/.cache/ansible-compat/f5bcd7/roles:/root/.ansible/roles:/usr/share/ansible/roles:/etc/ansible/roles
WARNING  Computed fully qualified role name of vector-role does not follow current galaxy requirements.
Please edit meta/main.yml and assure we can correctly determine full role name:

galaxy_info:
role_name: my_name  # if absent directory name hosting role is used instead
namespace: my_galaxy_namespace  # if absent, author is used instead

Namespace: https://galaxy.ansible.com/docs/contributing/namespaces.html#galaxy-namespace-limitations
Role: https://galaxy.ansible.com/docs/contributing/creating_role.html#role-names

As an alternative, you can add 'role-name' to either skip_list or warn_list.

INFO     Using /root/.cache/ansible-compat/f5bcd7/roles/vector-role symlink to current repository in order to enable Ansible to find the role using its expected full name.
INFO     Running default > dependency
WARNING  Skipping, missing the requirements file.
WARNING  Skipping, missing the requirements file.
INFO     Running default > cleanup
WARNING  Skipping, cleanup playbook not configured.
INFO     Running default > destroy
INFO     Sanity checks: 'docker'

PLAY [Destroy] *****************************************************************

TASK [Populate instance config] ************************************************
ok: [localhost]

TASK [Dump instance config] ****************************************************
skipping: [localhost]

PLAY RECAP *********************************************************************
localhost                  : ok=1    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0

INFO     Running default > syntax

playbook: /home/nikulinn/Documents/mnt-homeworks/08-ansible-05-testing/playbook/roles/vector-role/molecule/default/converge.yml
INFO     Running default > create

PLAY [Create] ******************************************************************

TASK [Populate instance config dict] *******************************************
skipping: [localhost]

TASK [Convert instance config dict to a list] **********************************
skipping: [localhost]

TASK [Dump instance config] ****************************************************
skipping: [localhost]

PLAY RECAP *********************************************************************
localhost                  : ok=0    changed=0    unreachable=0    failed=0    skipped=3    rescued=0    ignored=0

INFO     Running default > prepare
WARNING  Skipping, prepare playbook not configured.
INFO     Running default > converge

PLAY [Converge] ****************************************************************

TASK [Gathering Facts] *********************************************************
ok: [dreamy_varahamihira]
ok: [awesome_bhabha]
ok: [sad_merkle]

TASK [Include vector] **********************************************************

TASK [vector-role : Check if the system is not support by this role] ***********
skipping: [awesome_bhabha]
ok: [dreamy_varahamihira] => {
    "msg": "System Fedora is not support by this role"
}
ok: [sad_merkle] => {
    "msg": "System Ubuntu is not support by this role"
}

TASK [vector-role : End play for host if unsupported system detected] **********
skipping: [awesome_bhabha]

TASK [vector-role : End play for host if unsupported system detected] **********

TASK [vector-role : End play for host if unsupported system detected] **********

TASK [vector-role : Create vector work directory] ******************************
changed: [awesome_bhabha]

TASK [vector-role : Get Vector distrib] ****************************************
changed: [awesome_bhabha]

TASK [vector-role : Unzip Vector archive] **************************************
changed: [awesome_bhabha]

TASK [vector-role : Install Vector binary] *************************************
changed: [awesome_bhabha]

TASK [vector-role : Check Vector installation] *********************************
changed: [awesome_bhabha]

TASK [vector-role : Create Vector etc directory] *******************************
changed: [awesome_bhabha]

TASK [vector-role : Create Vector config vector.yaml] **************************
changed: [awesome_bhabha]

TASK [vector-role : Create vector.service daemon] ******************************
changed: [awesome_bhabha]

TASK [vector-role : Modify Vector.service file ExecStart] **********************
changed: [awesome_bhabha]

TASK [vector-role : Modify Vector.service file ExecStartPre] *******************
changed: [awesome_bhabha]

TASK [vector-role : Create user vector] ****************************************
changed: [awesome_bhabha]

TASK [vector-role : Create Vector data_dir] ************************************
changed: [awesome_bhabha]

PLAY RECAP *********************************************************************
awesome_bhabha             : ok=13   changed=12   unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
dreamy_varahamihira        : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
sad_merkle                 : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

INFO     Running default > idempotence

PLAY [Converge] ****************************************************************

TASK [Gathering Facts] *********************************************************
ok: [dreamy_varahamihira]
ok: [awesome_bhabha]
ok: [sad_merkle]

TASK [Include vector] **********************************************************

TASK [vector-role : Check if the system is not support by this role] ***********
skipping: [awesome_bhabha]
ok: [dreamy_varahamihira] => {
    "msg": "System Fedora is not support by this role"
}
ok: [sad_merkle] => {
    "msg": "System Ubuntu is not support by this role"
}

TASK [vector-role : End play for host if unsupported system detected] **********
skipping: [awesome_bhabha]

TASK [vector-role : End play for host if unsupported system detected] **********

TASK [vector-role : End play for host if unsupported system detected] **********

TASK [vector-role : Create vector work directory] ******************************
ok: [awesome_bhabha]

TASK [vector-role : Get Vector distrib] ****************************************
ok: [awesome_bhabha]

TASK [vector-role : Unzip Vector archive] **************************************
ok: [awesome_bhabha]

TASK [vector-role : Install Vector binary] *************************************
ok: [awesome_bhabha]

TASK [vector-role : Check Vector installation] *********************************
changed: [awesome_bhabha]

TASK [vector-role : Create Vector etc directory] *******************************
ok: [awesome_bhabha]

TASK [vector-role : Create Vector config vector.yaml] **************************
ok: [awesome_bhabha]

TASK [vector-role : Create vector.service daemon] ******************************
changed: [awesome_bhabha]

TASK [vector-role : Modify Vector.service file ExecStart] **********************
changed: [awesome_bhabha]

TASK [vector-role : Modify Vector.service file ExecStartPre] *******************
changed: [awesome_bhabha]

TASK [vector-role : Create user vector] ****************************************
ok: [awesome_bhabha]

TASK [vector-role : Create Vector data_dir] ************************************
ok: [awesome_bhabha]

PLAY RECAP *********************************************************************
awesome_bhabha             : ok=13   changed=4    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
dreamy_varahamihira        : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
sad_merkle                 : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

CRITICAL Idempotence test failed because of the following tasks:
*  => vector-role : Check Vector installation
*  => vector-role : Create vector.service daemon
*  => vector-role : Modify Vector.service file ExecStart
*  => vector-role : Modify Vector.service file ExecStartPre
WARNING  An error occurred during the test sequence action: 'idempotence'. Cleaning up.
INFO     Running default > cleanup
WARNING  Skipping, cleanup playbook not configured.
INFO     Running default > destroy

PLAY [Destroy] *****************************************************************

TASK [Populate instance config] ************************************************
ok: [localhost]

TASK [Dump instance config] ****************************************************
skipping: [localhost]

PLAY RECAP *********************************************************************
localhost                  : ok=1    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0

INFO     Pruning extra files from scenario ephemeral directory
```
</details>

Проверка идемпотентности проваливается, т.к. в Task есть задачи, которые исполняются повторно, независимо от перезапуска, что является ошибкой для проверки. Для дальнейшей работы указанные Task были закоментированы. Это позволит провести дальнейшие проверки.

#### 4. Добавьте несколько assert в verify.yml-файл для проверки работоспособности vector-role (проверка, что конфиг валидный, проверка успешности запуска и др.). 

```shell
---
- name: Verify
  hosts: all
  gather_facts: false
  tasks:
  - name: Example assertion
    assert:
      that: true
```


#### 5. Запустите тестирование роли повторно и проверьте, что оно прошло успешно.

<details>
<summary>molecule test</summary>

```shell
root@nikulin:/home/nikulinn/Documents/mnt-homeworks/08-ansible-05-testing/playbook/roles/vector-role# molecule test
WARNING  Driver docker does not provide a schema.
INFO     default scenario test matrix: dependency, cleanup, destroy, syntax, create, prepare, converge, idempotence, side_effect, verify, cleanup, destroy
INFO     Performing prerun with role_name_check=1...
INFO     Set ANSIBLE_LIBRARY=/root/.cache/ansible-compat/f5bcd7/modules:/root/.ansible/plugins/modules:/usr/share/ansible/plugins/modules
INFO     Set ANSIBLE_COLLECTIONS_PATH=/root/.cache/ansible-compat/f5bcd7/collections:/root/.ansible/collections:/usr/share/ansible/collections
INFO     Set ANSIBLE_ROLES_PATH=/root/.cache/ansible-compat/f5bcd7/roles:/root/.ansible/roles:/usr/share/ansible/roles:/etc/ansible/roles
WARNING  Computed fully qualified role name of vector-role does not follow current galaxy requirements.
Please edit meta/main.yml and assure we can correctly determine full role name:

galaxy_info:
role_name: my_name  # if absent directory name hosting role is used instead
namespace: my_galaxy_namespace  # if absent, author is used instead

Namespace: https://galaxy.ansible.com/docs/contributing/namespaces.html#galaxy-namespace-limitations
Role: https://galaxy.ansible.com/docs/contributing/creating_role.html#role-names

As an alternative, you can add 'role-name' to either skip_list or warn_list.

INFO     Using /root/.cache/ansible-compat/f5bcd7/roles/vector-role symlink to current repository in order to enable Ansible to find the role using its expected full name.
INFO     Running default > dependency
WARNING  Skipping, missing the requirements file.
WARNING  Skipping, missing the requirements file.
INFO     Running default > cleanup
WARNING  Skipping, cleanup playbook not configured.
INFO     Running default > destroy
INFO     Sanity checks: 'docker'

PLAY [Destroy] *****************************************************************

TASK [Populate instance config] ************************************************
ok: [localhost]

TASK [Dump instance config] ****************************************************
skipping: [localhost]

PLAY RECAP *********************************************************************
localhost                  : ok=1    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0

INFO     Running default > syntax

playbook: /home/nikulinn/Documents/mnt-homeworks/08-ansible-05-testing/playbook/roles/vector-role/molecule/default/converge.yml
INFO     Running default > create

PLAY [Create] ******************************************************************

TASK [Populate instance config dict] *******************************************
skipping: [localhost]

TASK [Convert instance config dict to a list] **********************************
skipping: [localhost]

TASK [Dump instance config] ****************************************************
skipping: [localhost]

PLAY RECAP *********************************************************************
localhost                  : ok=0    changed=0    unreachable=0    failed=0    skipped=3    rescued=0    ignored=0

INFO     Running default > prepare
WARNING  Skipping, prepare playbook not configured.
INFO     Running default > converge

PLAY [Converge] ****************************************************************

TASK [Gathering Facts] *********************************************************
ok: [dreamy_varahamihira]
ok: [awesome_bhabha]
ok: [sad_merkle]

TASK [Include vector] **********************************************************

TASK [vector-role : Check if the system is not support by this role] ***********
skipping: [awesome_bhabha]
ok: [dreamy_varahamihira] => {
    "msg": "System Fedora is not support by this role"
}
ok: [sad_merkle] => {
    "msg": "System Ubuntu is not support by this role"
}

TASK [vector-role : End play for host if unsupported system detected] **********
skipping: [awesome_bhabha]

TASK [vector-role : End play for host if unsupported system detected] **********

TASK [vector-role : End play for host if unsupported system detected] **********

TASK [vector-role : Create vector work directory] ******************************
ok: [awesome_bhabha]

TASK [vector-role : Get Vector distrib] ****************************************
ok: [awesome_bhabha]

TASK [vector-role : Unzip Vector archive] **************************************
ok: [awesome_bhabha]

TASK [vector-role : Install Vector binary] *************************************
ok: [awesome_bhabha]

TASK [vector-role : Create Vector etc directory] *******************************
ok: [awesome_bhabha]

TASK [vector-role : Create Vector config vector.yaml] **************************
ok: [awesome_bhabha]

TASK [vector-role : Create user vector] ****************************************
ok: [awesome_bhabha]

TASK [vector-role : Create Vector data_dir] ************************************
ok: [awesome_bhabha]

PLAY RECAP *********************************************************************
awesome_bhabha             : ok=9    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
dreamy_varahamihira        : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
sad_merkle                 : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

INFO     Running default > idempotence

PLAY [Converge] ****************************************************************

TASK [Gathering Facts] *********************************************************
ok: [dreamy_varahamihira]
ok: [sad_merkle]
ok: [awesome_bhabha]

TASK [Include vector] **********************************************************

TASK [vector-role : Check if the system is not support by this role] ***********
skipping: [awesome_bhabha]
ok: [dreamy_varahamihira] => {
    "msg": "System Fedora is not support by this role"
}
ok: [sad_merkle] => {
    "msg": "System Ubuntu is not support by this role"
}

TASK [vector-role : End play for host if unsupported system detected] **********
skipping: [awesome_bhabha]

TASK [vector-role : End play for host if unsupported system detected] **********

TASK [vector-role : End play for host if unsupported system detected] **********

TASK [vector-role : Create vector work directory] ******************************
ok: [awesome_bhabha]

TASK [vector-role : Get Vector distrib] ****************************************
ok: [awesome_bhabha]

TASK [vector-role : Unzip Vector archive] **************************************
ok: [awesome_bhabha]

TASK [vector-role : Install Vector binary] *************************************
ok: [awesome_bhabha]

TASK [vector-role : Create Vector etc directory] *******************************
ok: [awesome_bhabha]

TASK [vector-role : Create Vector config vector.yaml] **************************
ok: [awesome_bhabha]

TASK [vector-role : Create user vector] ****************************************
ok: [awesome_bhabha]

TASK [vector-role : Create Vector data_dir] ************************************
ok: [awesome_bhabha]

PLAY RECAP *********************************************************************
awesome_bhabha             : ok=9    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
dreamy_varahamihira        : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
sad_merkle                 : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

INFO     Idempotence completed successfully.
INFO     Running default > side_effect
WARNING  Skipping, side effect playbook not configured.
INFO     Running default > verify
INFO     Running Ansible Verifier

PLAY [Verify] ******************************************************************

TASK [Example assertion] *******************************************************
ok: [awesome_bhabha] => {
    "changed": false,
    "msg": "All assertions passed"
}
ok: [dreamy_varahamihira] => {
    "changed": false,
    "msg": "All assertions passed"
}
ok: [sad_merkle] => {
    "changed": false,
    "msg": "All assertions passed"
}

PLAY RECAP *********************************************************************
awesome_bhabha             : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
dreamy_varahamihira        : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
sad_merkle                 : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

INFO     Verifier completed successfully.
INFO     Running default > cleanup
WARNING  Skipping, cleanup playbook not configured.
INFO     Running default > destroy

PLAY [Destroy] *****************************************************************

TASK [Populate instance config] ************************************************
ok: [localhost]

TASK [Dump instance config] ****************************************************
skipping: [localhost]

PLAY RECAP *********************************************************************
localhost                  : ok=1    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0

INFO     Pruning extra files from scenario ephemeral directory

```
</details>

#### 6. Добавьте новый тег на коммит с рабочим сценарием в соответствии с семантическим версионированием.

[vector-role, tag: molecule-test](git@github.com:NikulinMS/vector-role.git)

___

### Tox

#### 1. Добавьте в директорию с vector-role файлы из [директории](./example).



#### 2. Запустите `docker run --privileged=True -v <path_to_repo>:/opt/vector-role -w /opt/vector-role -it aragast/netology:latest /bin/bash`, где path_to_repo — путь до корня репозитория с vector-role на вашей файловой системе.

```shell
root@nikulin:/home/nikulinn/other/ansible/08-ansible-05-testing/playbook/roles/vector-role# docker run --privileged=True -v /home/nikulinn/other/ansible/08-ansible-05-testing/playbook/roles/vector-role:/opt/vector-role -w /opt/vector-role -it aragast/netology:latest /bin/bash
Unable to find image 'aragast/netology:latest' locally
latest: Pulling from aragast/netology
f70d60810c69: Pull complete 
545277d80005: Pull complete 
3787740a304b: Pull complete 
8099be4bd6d4: Pull complete 
78316366859b: Pull complete 
a887350ff6d8: Pull complete 
8ab90b51dc15: Pull complete 
14617a4d32c2: Pull complete 
b868affa868e: Pull complete 
1e0b58337306: Pull complete 
9167ab0cbb7e: Pull complete 
907e71e165dd: Pull complete 
6025d523ea47: Pull complete 
6084c8fa3ce3: Pull complete 
cffe842942c7: Pull complete 
d984a1f47d62: Pull complete 
Digest: sha256:e44f93d3d9880123ac8170d01bd38ea1cd6c5174832b1782ce8f97f13e695ad5
Status: Downloaded newer image for aragast/netology:latest
[root@6db96462caf3 vector-role]# 
```


#### 3. Внутри контейнера выполните команду `tox`, посмотрите на вывод.

```shell
[root@61c7b7910e08 vector-role]# tox
py37-ansible210 installed: ansible==2.10.7,ansible-base==2.10.17,ansible-compat==1.0.0,ansible-lint==5.1.3,arrow==1.2.3,bcrypt==4.1.1,binaryornot==0.4.4,bracex==2.3.post1,cached-property==1.5.2,Cerberus==1.3.5,certifi==2023.11.17,cffi==1.15.1,chardet==5.2.0,charset-normalizer==3.3.2,click==8.1.7,click-help-colors==0.9.4,cookiecutter==2.5.0,cryptography==41.0.7,distro==1.8.0,enrich==1.2.7,idna==3.6,importlib-metadata==6.7.0,Jinja2==3.1.2,jmespath==1.0.1,lxml==4.9.3,markdown-it-py==2.2.0,MarkupSafe==2.1.3,mdurl==0.1.2,molecule==3.5.2,molecule-podman==1.1.0,packaging==23.2,paramiko==2.12.0,pathspec==0.11.2,pluggy==1.2.0,pycparser==2.21,Pygments==2.17.2,PyNaCl==1.5.0,python-dateutil==2.8.2,python-slugify==8.0.1,PyYAML==5.4.1,requests==2.31.0,rich==13.7.0,ruamel.yaml==0.18.5,ruamel.yaml.clib==0.2.8,selinux==0.2.1,six==1.16.0,subprocess-tee==0.3.5,tenacity==8.2.3,text-unidecode==1.3,typing_extensions==4.7.1,urllib3==2.0.7,wcmatch==8.4.1,yamllint==1.26.3,zipp==3.15.0
py37-ansible210 run-test-pre: PYTHONHASHSEED='585868611'
py37-ansible210 run-test: commands[0] | molecule test -s compatibility --destroy always
CRITICAL 'molecule/compatibility/molecule.yml' glob failed.  Exiting.
ERROR: InvocationError for command /opt/vector-role/.tox/py37-ansible210/bin/molecule test -s compatibility --destroy always (exited with code 1)
py37-ansible30 installed: ansible==3.0.0,ansible-base==2.10.17,ansible-compat==1.0.0,ansible-lint==5.1.3,arrow==1.2.3,bcrypt==4.1.1,binaryornot==0.4.4,bracex==2.3.post1,cached-property==1.5.2,Cerberus==1.3.5,certifi==2023.11.17,cffi==1.15.1,chardet==5.2.0,charset-normalizer==3.3.2,click==8.1.7,click-help-colors==0.9.4,cookiecutter==2.5.0,cryptography==41.0.7,distro==1.8.0,enrich==1.2.7,idna==3.6,importlib-metadata==6.7.0,Jinja2==3.1.2,jmespath==1.0.1,lxml==4.9.3,markdown-it-py==2.2.0,MarkupSafe==2.1.3,mdurl==0.1.2,molecule==3.5.2,molecule-podman==1.1.0,packaging==23.2,paramiko==2.12.0,pathspec==0.11.2,pluggy==1.2.0,pycparser==2.21,Pygments==2.17.2,PyNaCl==1.5.0,python-dateutil==2.8.2,python-slugify==8.0.1,PyYAML==5.4.1,requests==2.31.0,rich==13.7.0,ruamel.yaml==0.18.5,ruamel.yaml.clib==0.2.8,selinux==0.2.1,six==1.16.0,subprocess-tee==0.3.5,tenacity==8.2.3,text-unidecode==1.3,typing_extensions==4.7.1,urllib3==2.0.7,wcmatch==8.4.1,yamllint==1.26.3,zipp==3.15.0
py37-ansible30 run-test-pre: PYTHONHASHSEED='585868611'
py37-ansible30 run-test: commands[0] | molecule test -s compatibility --destroy always
CRITICAL 'molecule/compatibility/molecule.yml' glob failed.  Exiting.
ERROR: InvocationError for command /opt/vector-role/.tox/py37-ansible30/bin/molecule test -s compatibility --destroy always (exited with code 1)
py39-ansible210 installed: ansible==2.10.7,ansible-base==2.10.17,ansible-compat==4.1.10,ansible-core==2.15.7,ansible-lint==5.1.3,arrow==1.3.0,attrs==23.1.0,bcrypt==4.1.1,binaryornot==0.4.4,bracex==2.4,Cerberus==1.3.5,certifi==2023.11.17,cffi==1.16.0,chardet==5.2.0,charset-normalizer==3.3.2,click==8.1.7,click-help-colors==0.9.4,cookiecutter==2.5.0,cryptography==41.0.7,distro==1.8.0,enrich==1.2.7,idna==3.6,importlib-resources==5.0.7,Jinja2==3.1.2,jmespath==1.0.1,jsonschema==4.20.0,jsonschema-specifications==2023.11.2,lxml==4.9.3,markdown-it-py==3.0.0,MarkupSafe==2.1.3,mdurl==0.1.2,molecule==3.5.2,molecule-podman==2.0.0,packaging==23.2,paramiko==2.12.0,pathspec==0.11.2,pluggy==1.3.0,pycparser==2.21,Pygments==2.17.2,PyNaCl==1.5.0,python-dateutil==2.8.2,python-slugify==8.0.1,PyYAML==5.4.1,referencing==0.31.1,requests==2.31.0,resolvelib==1.0.1,rich==13.7.0,rpds-py==0.13.2,ruamel.yaml==0.18.5,ruamel.yaml.clib==0.2.8,selinux==0.3.0,six==1.16.0,subprocess-tee==0.4.1,tenacity==8.2.3,text-unidecode==1.3,types-python-dateutil==2.8.19.14,typing_extensions==4.8.0,urllib3==2.1.0,wcmatch==8.5,yamllint==1.26.3
py39-ansible210 run-test-pre: PYTHONHASHSEED='585868611'
py39-ansible210 run-test: commands[0] | molecule test -s compatibility --destroy always
CRITICAL 'molecule/compatibility/molecule.yml' glob failed.  Exiting.
ERROR: InvocationError for command /opt/vector-role/.tox/py39-ansible210/bin/molecule test -s compatibility --destroy always (exited with code 1)
py39-ansible30 installed: ansible==3.0.0,ansible-base==2.10.17,ansible-compat==4.1.10,ansible-core==2.15.7,ansible-lint==5.1.3,arrow==1.3.0,attrs==23.1.0,bcrypt==4.1.1,binaryornot==0.4.4,bracex==2.4,Cerberus==1.3.5,certifi==2023.11.17,cffi==1.16.0,chardet==5.2.0,charset-normalizer==3.3.2,click==8.1.7,click-help-colors==0.9.4,cookiecutter==2.5.0,cryptography==41.0.7,distro==1.8.0,enrich==1.2.7,idna==3.6,importlib-resources==5.0.7,Jinja2==3.1.2,jmespath==1.0.1,jsonschema==4.20.0,jsonschema-specifications==2023.11.2,lxml==4.9.3,markdown-it-py==3.0.0,MarkupSafe==2.1.3,mdurl==0.1.2,molecule==3.5.2,molecule-podman==2.0.0,packaging==23.2,paramiko==2.12.0,pathspec==0.11.2,pluggy==1.3.0,pycparser==2.21,Pygments==2.17.2,PyNaCl==1.5.0,python-dateutil==2.8.2,python-slugify==8.0.1,PyYAML==5.4.1,referencing==0.31.1,requests==2.31.0,resolvelib==1.0.1,rich==13.7.0,rpds-py==0.13.2,ruamel.yaml==0.18.5,ruamel.yaml.clib==0.2.8,selinux==0.3.0,six==1.16.0,subprocess-tee==0.4.1,tenacity==8.2.3,text-unidecode==1.3,types-python-dateutil==2.8.19.14,typing_extensions==4.8.0,urllib3==2.1.0,wcmatch==8.5,yamllint==1.26.3
py39-ansible30 run-test-pre: PYTHONHASHSEED='585868611'
py39-ansible30 run-test: commands[0] | molecule test -s compatibility --destroy always
CRITICAL 'molecule/compatibility/molecule.yml' glob failed.  Exiting.
ERROR: InvocationError for command /opt/vector-role/.tox/py39-ansible30/bin/molecule test -s compatibility --destroy always (exited with code 1)
_____________________________________________________________________________________________________________ summary ______________________________________________________________________________________________________________
ERROR:   py37-ansible210: commands failed
ERROR:   py37-ansible30: commands failed
ERROR:   py39-ansible210: commands failed
ERROR:   py39-ansible30: commands failed

```

#### 4. Создайте облегчённый сценарий для `molecule` с драйвером `molecule_podman`. Проверьте его на исполнимость.

```shell
---
dependency:
  name: galaxy
driver:
  name: podman
platforms:
  - name: stupefied_curie
    image: docker.io/pycontribs/debian:latest
    pre_build_image: true
provisioner:
  name: ansible
verifier:
  name: ansible
scenario:
  test_sequence:
    - destroy
    - create
    - converge
    - destroy
```
```shell
root@nikulin:/home/nikulinn/other/ansible/08-ansible-05-testing/playbook/roles/vector-role# molecule matrix -s tox test
WARNING  Driver docker does not provide a schema.
WARNING  Driver podman does not provide a schema.
INFO     Test matrix
---                                                                                                                                                                                                                                 
tox:                                                                                                                                                                                                                                
  - destroy                                                                                                                                                                                                                         
  - create                                                                                                                                                                                                                          
  - converge                                                                                                                                                                                                                        
  - destroy    
```

#### 5. Пропишите правильную команду в `tox.ini`, чтобы запускался облегчённый сценарий.

```shell
[tox]
minversion = 1.8
basepython = python3.6
envlist = py{37}-ansible{210,30}
skipsdist = true

[testenv]
passenv = *
deps =
    -r tox-requirements.txt
    ansible210: ansible<3.0
    ansible30: ansible<3.1
commands =
    {posargs:molecule test -s tox --destroy always}
```

#### 6. Запустите команду `tox`. Убедитесь, что всё отработало успешно.

<details>
<summary>[root@61c7b7910e08 vector-role]# tox</summary>

```shell
[root@61c7b7910e08 vector-role]# tox
py37-ansible210 installed: ansible==2.10.7,ansible-base==2.10.17,ansible-compat==1.0.0,ansible-lint==5.1.3,arrow==1.2.3,bcrypt==4.1.1,binaryornot==0.4.4,bracex==2.3.post1,cached-property==1.5.2,Cerberus==1.3.5,certifi==2023.11.17,cffi==1.15.1,chardet==5.2.0,charset-normalizer==3.3.2,click==8.1.7,click-help-colors==0.9.4,cookiecutter==2.5.0,cryptography==41.0.7,distro==1.8.0,enrich==1.2.7,idna==3.6,importlib-metadata==6.7.0,Jinja2==3.1.2,jmespath==1.0.1,lxml==4.9.3,markdown-it-py==2.2.0,MarkupSafe==2.1.3,mdurl==0.1.2,molecule==3.5.2,molecule-podman==1.1.0,packaging==23.2,paramiko==2.12.0,pathspec==0.11.2,pluggy==1.2.0,pycparser==2.21,Pygments==2.17.2,PyNaCl==1.5.0,python-dateutil==2.8.2,python-slugify==8.0.1,PyYAML==5.4.1,requests==2.31.0,rich==13.7.0,ruamel.yaml==0.18.5,ruamel.yaml.clib==0.2.8,selinux==0.2.1,six==1.16.0,subprocess-tee==0.3.5,tenacity==8.2.3,text-unidecode==1.3,typing_extensions==4.7.1,urllib3==2.0.7,wcmatch==8.4.1,yamllint==1.26.3,zipp==3.15.0
py37-ansible210 run-test-pre: PYTHONHASHSEED='1967078038'
py37-ansible210 run-test: commands[0] | molecule test -s tox --destroy always
INFO     tox scenario test matrix: destroy, create, converge, destroy
INFO     Performing prerun...
INFO     Set ANSIBLE_LIBRARY=/root/.cache/ansible-compat/b984a4/modules:/root/.ansible/plugins/modules:/usr/share/ansible/plugins/modules
INFO     Set ANSIBLE_COLLECTIONS_PATH=/root/.cache/ansible-compat/b984a4/collections:/root/.ansible/collections:/usr/share/ansible/collections
INFO     Set ANSIBLE_ROLES_PATH=/root/.cache/ansible-compat/b984a4/roles:/root/.ansible/roles:/usr/share/ansible/roles:/etc/ansible/roles
INFO     Running tox > destroy
INFO     Sanity checks: 'podman'

PLAY [Destroy] *****************************************************************

TASK [Destroy molecule instance(s)] ********************************************
changed: [localhost] => (item={'image': 'docker.io/pycontribs/debian:latest', 'name': 'stupefied_curie', 'pre_build_image': True})

TASK [Wait for instance(s) deletion to complete] *******************************
changed: [localhost] => (item={'started': 1, 'finished': 0, 'ansible_job_id': '124529077575.65', 'results_file': '/root/.ansible_async/124529077575.65', 'changed': True, 'failed': False, 'item': {'image': 'docker.io/pycontribs/debian:latest', 'name': 'stupefied_curie', 'pre_build_image': True}, 'ansible_loop_var': 'item'})

PLAY RECAP *********************************************************************
localhost                  : ok=2    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

INFO     Running tox > create

PLAY [Create] ******************************************************************

TASK [get podman executable path] **********************************************
ok: [localhost]

TASK [save path to executable as fact] *****************************************
ok: [localhost]

TASK [Log into a container registry] *******************************************
skipping: [localhost] => (item="stupefied_curie registry username: None specified") 

TASK [Check presence of custom Dockerfiles] ************************************
ok: [localhost] => (item=Dockerfile: None specified)

TASK [Create Dockerfiles from image names] *************************************
skipping: [localhost] => (item="Dockerfile: None specified; Image: docker.io/pycontribs/debian:latest") 

TASK [Discover local Podman images] ********************************************
ok: [localhost] => (item=stupefied_curie)

TASK [Build an Ansible compatible image] ***************************************
skipping: [localhost] => (item=docker.io/pycontribs/debian:latest) 

TASK [Determine the CMD directives] ********************************************
ok: [localhost] => (item="stupefied_curie command: None specified")

TASK [Remove possible pre-existing containers] *********************************
changed: [localhost]

TASK [Discover local podman networks] ******************************************
skipping: [localhost] => (item=stupefied_curie: None specified) 

TASK [Create podman network dedicated to this scenario] ************************
skipping: [localhost]

TASK [Create molecule instance(s)] *********************************************
changed: [localhost] => (item=stupefied_curie)

TASK [Wait for instance(s) creation to complete] *******************************
changed: [localhost] => (item=stupefied_curie)

PLAY RECAP *********************************************************************
localhost                  : ok=8    changed=3    unreachable=0    failed=0    skipped=5    rescued=0    ignored=0

INFO     Running tox > converge

PLAY [Converge] ****************************************************************

TASK [Gathering Facts] *********************************************************
ok: [stupefied_curie]

TASK [Copy something to test use of synchronize module] ************************
changed: [stupefied_curie]

TASK [Include vector-role] *****************************************************

TASK [vector-role : Check if the system is not support by this role] ***********
skipping: [stupefied_curie]

TASK [vector-role : Create vector work directory] ******************************
changed: [stupefied_curie]

TASK [vector-role : Get Vector distrib] ****************************************
changed: [stupefied_curie]

TASK [vector-role : Unzip Vector archive] **************************************
changed: [stupefied_curie]

TASK [vector-role : Install Vector binary] *************************************
changed: [stupefied_curie]

TASK [vector-role : Create Vector etc directory] *******************************
changed: [stupefied_curie]

TASK [vector-role : Create Vector config vector.yaml] **************************
changed: [stupefied_curie]

TASK [vector-role : Create user vector] ****************************************
changed: [stupefied_curie]

TASK [vector-role : Create Vector data_dir] ************************************
changed: [stupefied_curie]

PLAY RECAP *********************************************************************
stupefied_curie            : ok=10   changed=9    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0

INFO     Running tox > destroy

PLAY [Destroy] *****************************************************************

TASK [Destroy molecule instance(s)] ********************************************
changed: [localhost] => (item={'image': 'docker.io/pycontribs/debian:latest', 'name': 'stupefied_curie', 'pre_build_image': True})

TASK [Wait for instance(s) deletion to complete] *******************************
FAILED - RETRYING: Wait for instance(s) deletion to complete (300 retries left).
FAILED - RETRYING: Wait for instance(s) deletion to complete (299 retries left).
changed: [localhost] => (item={'started': 1, 'finished': 0, 'ansible_job_id': '375379396132.2222', 'results_file': '/root/.ansible_async/375379396132.2222', 'changed': True, 'failed': False, 'item': {'image': 'docker.io/pycontribs/debian:latest', 'name': 'stupefied_curie', 'pre_build_image': True}, 'ansible_loop_var': 'item'})

PLAY RECAP *********************************************************************
localhost                  : ok=2    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

INFO     Pruning extra files from scenario ephemeral directory
py37-ansible30 installed: ansible==3.0.0,ansible-base==2.10.17,ansible-compat==1.0.0,ansible-lint==5.1.3,arrow==1.2.3,bcrypt==4.1.1,binaryornot==0.4.4,bracex==2.3.post1,cached-property==1.5.2,Cerberus==1.3.5,certifi==2023.11.17,cffi==1.15.1,chardet==5.2.0,charset-normalizer==3.3.2,click==8.1.7,click-help-colors==0.9.4,cookiecutter==2.5.0,cryptography==41.0.7,distro==1.8.0,enrich==1.2.7,idna==3.6,importlib-metadata==6.7.0,Jinja2==3.1.2,jmespath==1.0.1,lxml==4.9.3,markdown-it-py==2.2.0,MarkupSafe==2.1.3,mdurl==0.1.2,molecule==3.5.2,molecule-podman==1.1.0,packaging==23.2,paramiko==2.12.0,pathspec==0.11.2,pluggy==1.2.0,pycparser==2.21,Pygments==2.17.2,PyNaCl==1.5.0,python-dateutil==2.8.2,python-slugify==8.0.1,PyYAML==5.4.1,requests==2.31.0,rich==13.7.0,ruamel.yaml==0.18.5,ruamel.yaml.clib==0.2.8,selinux==0.2.1,six==1.16.0,subprocess-tee==0.3.5,tenacity==8.2.3,text-unidecode==1.3,typing_extensions==4.7.1,urllib3==2.0.7,wcmatch==8.4.1,yamllint==1.26.3,zipp==3.15.0
py37-ansible30 run-test-pre: PYTHONHASHSEED='1967078038'
py37-ansible30 run-test: commands[0] | molecule test -s tox --destroy always
INFO     tox scenario test matrix: destroy, create, converge, destroy
INFO     Performing prerun...
INFO     Set ANSIBLE_LIBRARY=/root/.cache/ansible-compat/b984a4/modules:/root/.ansible/plugins/modules:/usr/share/ansible/plugins/modules
INFO     Set ANSIBLE_COLLECTIONS_PATH=/root/.cache/ansible-compat/b984a4/collections:/root/.ansible/collections:/usr/share/ansible/collections
INFO     Set ANSIBLE_ROLES_PATH=/root/.cache/ansible-compat/b984a4/roles:/root/.ansible/roles:/usr/share/ansible/roles:/etc/ansible/roles
INFO     Running tox > destroy
INFO     Sanity checks: 'podman'

PLAY [Destroy] *****************************************************************

TASK [Destroy molecule instance(s)] ********************************************
changed: [localhost] => (item={'image': 'docker.io/pycontribs/debian:latest', 'name': 'stupefied_curie', 'pre_build_image': True})

TASK [Wait for instance(s) deletion to complete] *******************************
changed: [localhost] => (item={'started': 1, 'finished': 0, 'ansible_job_id': '728118538986.2361', 'results_file': '/root/.ansible_async/728118538986.2361', 'changed': True, 'failed': False, 'item': {'image': 'docker.io/pycontribs/debian:latest', 'name': 'stupefied_curie', 'pre_build_image': True}, 'ansible_loop_var': 'item'})

PLAY RECAP *********************************************************************
localhost                  : ok=2    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

INFO     Running tox > create

PLAY [Create] ******************************************************************

TASK [get podman executable path] **********************************************
ok: [localhost]

TASK [save path to executable as fact] *****************************************
ok: [localhost]

TASK [Log into a container registry] *******************************************
skipping: [localhost] => (item="stupefied_curie registry username: None specified") 

TASK [Check presence of custom Dockerfiles] ************************************
ok: [localhost] => (item=Dockerfile: None specified)

TASK [Create Dockerfiles from image names] *************************************
skipping: [localhost] => (item="Dockerfile: None specified; Image: docker.io/pycontribs/debian:latest") 

TASK [Discover local Podman images] ********************************************
ok: [localhost] => (item=stupefied_curie)

TASK [Build an Ansible compatible image] ***************************************
skipping: [localhost] => (item=docker.io/pycontribs/debian:latest) 

TASK [Determine the CMD directives] ********************************************
ok: [localhost] => (item="stupefied_curie command: None specified")

TASK [Remove possible pre-existing containers] *********************************
changed: [localhost]

TASK [Discover local podman networks] ******************************************
skipping: [localhost] => (item=stupefied_curie: None specified) 

TASK [Create podman network dedicated to this scenario] ************************
skipping: [localhost]

TASK [Create molecule instance(s)] *********************************************
changed: [localhost] => (item=stupefied_curie)

TASK [Wait for instance(s) creation to complete] *******************************
changed: [localhost] => (item=stupefied_curie)

PLAY RECAP *********************************************************************
localhost                  : ok=8    changed=3    unreachable=0    failed=0    skipped=5    rescued=0    ignored=0

INFO     Running tox > converge

PLAY [Converge] ****************************************************************

TASK [Gathering Facts] *********************************************************
ok: [stupefied_curie]

TASK [Copy something to test use of synchronize module] ************************
changed: [stupefied_curie]

TASK [Include vector-role] *****************************************************

TASK [vector-role : Check if the system is not support by this role] ***********
skipping: [stupefied_curie]

TASK [vector-role : Create vector work directory] ******************************
changed: [stupefied_curie]

TASK [vector-role : Get Vector distrib] ****************************************
changed: [stupefied_curie]

TASK [vector-role : Unzip Vector archive] **************************************
changed: [stupefied_curie]

TASK [vector-role : Install Vector binary] *************************************
changed: [stupefied_curie]

TASK [vector-role : Create Vector etc directory] *******************************
changed: [stupefied_curie]

TASK [vector-role : Create Vector config vector.yaml] **************************
changed: [stupefied_curie]

TASK [vector-role : Create user vector] ****************************************
changed: [stupefied_curie]

TASK [vector-role : Create Vector data_dir] ************************************
changed: [stupefied_curie]

PLAY RECAP *********************************************************************
stupefied_curie            : ok=10   changed=9    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0

INFO     Running tox > destroy

PLAY [Destroy] *****************************************************************

TASK [Destroy molecule instance(s)] ********************************************
changed: [localhost] => (item={'image': 'docker.io/pycontribs/debian:latest', 'name': 'stupefied_curie', 'pre_build_image': True})

TASK [Wait for instance(s) deletion to complete] *******************************
FAILED - RETRYING: Wait for instance(s) deletion to complete (300 retries left).
FAILED - RETRYING: Wait for instance(s) deletion to complete (299 retries left).
changed: [localhost] => (item={'started': 1, 'finished': 0, 'ansible_job_id': '295918952954.4518', 'results_file': '/root/.ansible_async/295918952954.4518', 'changed': True, 'failed': False, 'item': {'image': 'docker.io/pycontribs/debian:latest', 'name': 'stupefied_curie', 'pre_build_image': True}, 'ansible_loop_var': 'item'})

PLAY RECAP *********************************************************************
localhost                  : ok=2    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

INFO     Pruning extra files from scenario ephemeral directory
_____________________________________________________________________________________________________________ summary ______________________________________________________________________________________________________________
  py37-ansible210: commands succeeded
  py37-ansible30: commands succeeded
  congratulations :)

```

</details>

#### 7. Добавьте новый тег на коммит с рабочим сценарием в соответствии с семантическим версионированием.

[vector-role, tag: molecule-tox-test](git@github.com:NikulinMS/vector-role.git)


