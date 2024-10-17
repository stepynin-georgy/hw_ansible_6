# Домашнее задание к занятию 6 «Создание собственных модулей»

## Подготовка к выполнению

1. Создайте пустой публичный репозиторий в своём любом проекте: `my_own_collection`.
2. Скачайте репозиторий Ansible: `git clone https://github.com/ansible/ansible.git` по любому, удобному вам пути.
3. Зайдите в директорию Ansible: `cd ansible`.
4. Создайте виртуальное окружение: `python3 -m venv venv`.
5. Активируйте виртуальное окружение: `. venv/bin/activate`. Дальнейшие действия производятся только в виртуальном окружении.
6. Установите зависимости `pip install -r requirements.txt`.

```
root@ansible-ubuntu:/opt/hw_ansible_5/ansible# python3.12 -m venv venv
root@ansible-ubuntu:/opt/hw_ansible_5/ansible# source venv/bin/activate
(venv) root@ansible-ubuntu:/opt/hw_ansible_5/ansible# pip install -r requirements.txt
Collecting jinja2>=3.0.0 (from -r requirements.txt (line 6))
  Using cached jinja2-3.1.4-py3-none-any.whl.metadata (2.6 kB)
Collecting PyYAML>=5.1 (from -r requirements.txt (line 7))
  Using cached PyYAML-6.0.2-cp312-cp312-manylinux_2_17_x86_64.manylinux2014_x86_64.whl.metadata (2.1 kB)
Collecting cryptography (from -r requirements.txt (line 8))
  Using cached cryptography-43.0.1-cp39-abi3-manylinux_2_28_x86_64.whl.metadata (5.4 kB)
Collecting packaging (from -r requirements.txt (line 9))
  Using cached packaging-24.1-py3-none-any.whl.metadata (3.2 kB)
Collecting resolvelib<1.1.0,>=0.5.3 (from -r requirements.txt (line 15))
  Using cached resolvelib-1.0.1-py2.py3-none-any.whl.metadata (4.0 kB)
Collecting MarkupSafe>=2.0 (from jinja2>=3.0.0->-r requirements.txt (line 6))
  Downloading MarkupSafe-3.0.0-cp312-cp312-manylinux_2_17_x86_64.manylinux2014_x86_64.whl.metadata (4.0 kB)
Collecting cffi>=1.12 (from cryptography->-r requirements.txt (line 8))
  Using cached cffi-1.17.1-cp312-cp312-manylinux_2_17_x86_64.manylinux2014_x86_64.whl.metadata (1.5 kB)
Collecting pycparser (from cffi>=1.12->cryptography->-r requirements.txt (line 8))
  Using cached pycparser-2.22-py3-none-any.whl.metadata (943 bytes)
Using cached jinja2-3.1.4-py3-none-any.whl (133 kB)
Using cached PyYAML-6.0.2-cp312-cp312-manylinux_2_17_x86_64.manylinux2014_x86_64.whl (767 kB)
Using cached cryptography-43.0.1-cp39-abi3-manylinux_2_28_x86_64.whl (4.0 MB)
Using cached packaging-24.1-py3-none-any.whl (53 kB)
Using cached resolvelib-1.0.1-py2.py3-none-any.whl (17 kB)
Using cached cffi-1.17.1-cp312-cp312-manylinux_2_17_x86_64.manylinux2014_x86_64.whl (479 kB)
Downloading MarkupSafe-3.0.0-cp312-cp312-manylinux_2_17_x86_64.manylinux2014_x86_64.whl (23 kB)
Using cached pycparser-2.22-py3-none-any.whl (117 kB)
Installing collected packages: resolvelib, PyYAML, pycparser, packaging, MarkupSafe, jinja2, cffi, cryptography
Successfully installed MarkupSafe-3.0.0 PyYAML-6.0.2 cffi-1.17.1 cryptography-43.0.1 jinja2-3.1.4 packaging-24.1 pycparser-2.22 resolvelib-1.0.1
(venv) root@ansible-ubuntu:/opt/hw_ansible_5/ansible#
```

7. Запустите настройку окружения `. hacking/env-setup`.

```
(venv) root@ansible-ubuntu:/opt/hw_ansible_5/ansible# . hacking/env-setup

Setting up Ansible to run out of checkout...

PATH=/opt/hw_ansible_5/ansible/bin:/opt/hw_ansible_5/ansible/venv/bin:/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
PYTHONPATH=/opt/hw_ansible_5/ansible/test/lib:/opt/hw_ansible_5/ansible/lib
MANPATH=/opt/hw_ansible_5/ansible/docs/man:/usr/share/man:/usr/local/man:/usr/local/share/man

Remember, you may wish to specify your host file with -i

Done!
```

8. Если все шаги прошли успешно — выйдите из виртуального окружения `deactivate`.
9. Ваше окружение настроено. Чтобы запустить его, нужно находиться в директории `ansible` и выполнить конструкцию `. venv/bin/activate && . hacking/env-setup`.

## Основная часть

Ваша цель — написать собственный module, который вы можете использовать в своей role через playbook. Всё это должно быть собрано в виде collection и отправлено в ваш репозиторий.

**Шаг 1.** В виртуальном окружении создайте новый `my_own_module.py` файл.

**Шаг 2.** Наполните его содержимым:

```python
#!/usr/bin/python

# Copyright: (c) 2018, Terry Jones <terry.jones@example.org>
# GNU General Public License v3.0+ (see COPYING or https://www.gnu.org/licenses/gpl-3.0.txt)
from __future__ import (absolute_import, division, print_function)
__metaclass__ = type

DOCUMENTATION = r'''
---
module: my_test

short_description: This is my test module

# If this is part of a collection, you need to use semantic versioning,
# i.e. the version is of the form "2.5.0" and not "2.4".
version_added: "1.0.0"

description: This is my longer description explaining my test module.

options:
    name:
        description: This is the message to send to the test module.
        required: true
        type: str
    new:
        description:
            - Control to demo if the result of this module is changed or not.
            - Parameter description can be a list as well.
        required: false
        type: bool
# Specify this value according to your collection
# in format of namespace.collection.doc_fragment_name
extends_documentation_fragment:
    - my_namespace.my_collection.my_doc_fragment_name

author:
    - Your Name (@yourGitHubHandle)
'''

EXAMPLES = r'''
# Pass in a message
- name: Test with a message
  my_namespace.my_collection.my_test:
    name: hello world

# pass in a message and have changed true
- name: Test with a message and changed output
  my_namespace.my_collection.my_test:
    name: hello world
    new: true

# fail the module
- name: Test failure of the module
  my_namespace.my_collection.my_test:
    name: fail me
'''

RETURN = r'''
# These are examples of possible return values, and in general should use other names for return values.
original_message:
    description: The original name param that was passed in.
    type: str
    returned: always
    sample: 'hello world'
message:
    description: The output message that the test module generates.
    type: str
    returned: always
    sample: 'goodbye'
'''

from ansible.module_utils.basic import AnsibleModule


def run_module():
    # define available arguments/parameters a user can pass to the module
    module_args = dict(
        name=dict(type='str', required=True),
        new=dict(type='bool', required=False, default=False)
    )

    # seed the result dict in the object
    # we primarily care about changed and state
    # changed is if this module effectively modified the target
    # state will include any data that you want your module to pass back
    # for consumption, for example, in a subsequent task
    result = dict(
        changed=False,
        original_message='',
        message=''
    )

    # the AnsibleModule object will be our abstraction working with Ansible
    # this includes instantiation, a couple of common attr would be the
    # args/params passed to the execution, as well as if the module
    # supports check mode
    module = AnsibleModule(
        argument_spec=module_args,
        supports_check_mode=True
    )

    # if the user is working with this module in only check mode we do not
    # want to make any changes to the environment, just return the current
    # state with no modifications
    if module.check_mode:
        module.exit_json(**result)

    # manipulate or modify the state as needed (this is going to be the
    # part where your module will do what it needs to do)
    result['original_message'] = module.params['name']
    result['message'] = 'goodbye'

    # use whatever logic you need to determine whether or not this module
    # made any modifications to your target
    if module.params['new']:
        result['changed'] = True

    # during the execution of the module, if there is an exception or a
    # conditional state that effectively causes a failure, run
    # AnsibleModule.fail_json() to pass in the message and the result
    if module.params['name'] == 'fail me':
        module.fail_json(msg='You requested this to fail', **result)

    # in the event of a successful module execution, you will want to
    # simple AnsibleModule.exit_json(), passing the key/value results
    module.exit_json(**result)


def main():
    run_module()


if __name__ == '__main__':
    main()
```
Или возьмите это наполнение [из статьи](https://docs.ansible.com/ansible/latest/dev_guide/developing_modules_general.html#creating-a-module).

**Шаг 3.** Заполните файл в соответствии с требованиями Ansible так, чтобы он выполнял основную задачу: module должен создавать текстовый файл на удалённом хосте по пути, определённом в параметре `path`, с содержимым, определённым в параметре `content`.

```
{
    "ANSIBLE_MODULE_ARGS": {
        "path": "/opt/test.txt",
        "content": "test message"
    }
}
```

**Шаг 4.** Проверьте module на исполняемость локально.

![изображение](https://github.com/stepynin-georgy/hw_ansible_6/blob/main/img/Screenshot_200.png)

**Шаг 5.** Напишите single task playbook и используйте module в нём.

[single_task_playbook.yaml](https://github.com/stepynin-georgy/hw_ansible_6/blob/main/single_task_playbook.yaml)

**Шаг 6.** Проверьте через playbook на идемпотентность.

```
(venv) root@ansible-ubuntu:/opt/hw_ansible_6# ansible-playbook single_task_playbook.yaml -v
[WARNING]: You are running the development version of Ansible. You should only run Ansible from "devel" if you are modifying the Ansible engine, or trying out features
under development. This is a rapidly changing source of code and can become unstable at any point.
No config file found; using defaults
[WARNING]: No inventory was parsed, only implicit localhost is available
[WARNING]: provided hosts list is empty, only localhost is available. Note that the implicit localhost does not match 'all'

PLAY [Test my module] ****************************************************************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************************************************************
ok: [localhost]

TASK [Create file] *******************************************************************************************************************************************************
changed: [localhost] => {"changed": true, "msg": "File ./test_file.txt with content \"This test content\" created successfully"}

PLAY RECAP ***************************************************************************************************************************************************************
localhost                  : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0


```

**Шаг 7.** Выйдите из виртуального окружения.

**Шаг 8.** Инициализируйте новую collection: `ansible-galaxy collection init my_own_namespace.yandex_cloud_elk`.

```
root@ansible-ubuntu:/opt/hw_ansible_6/ansible# ansible-galaxy collection init my_own_namespace.yandex_cloud_elk
[WARNING]: You are running the development version of Ansible. You should only run Ansible from "devel" if you are modifying the Ansible engine, or trying out features
under development. This is a rapidly changing source of code and can become unstable at any point.
- Collection my_own_namespace.yandex_cloud_elk was created successfully
```

**Шаг 9.** В эту collection перенесите свой module в соответствующую директорию.

```
root@ansible-ubuntu:/opt/hw_ansible_6/ansible# ls -l  my_own_namespace/yandex_cloud_elk/plugins/modules
total 8
-rw-r--r-- 1 root root 5075 Oct 17 14:11 my_own_module.py
```

**Шаг 10.** Single task playbook преобразуйте в single task role и перенесите в collection. У role должны быть default всех параметров module.

```
root@ansible-ubuntu:/opt/hw_ansible_6/ansible# cd my_own_namespace/yandex_cloud_elk/roles
root@ansible-ubuntu:/opt/hw_ansible_6/ansible/my_own_namespace/yandex_cloud_elk/roles# ansible-galaxy role init my_role
[WARNING]: You are running the development version of Ansible. You should only run Ansible from "devel" if you are modifying the Ansible engine, or trying out features
under development. This is a rapidly changing source of code and can become unstable at any point.
- Role my_role was created successfully
root@ansible-ubuntu:/opt/hw_ansible_6/ansible/my_own_namespace/yandex_cloud_elk/roles#
```

**Шаг 11.** Создайте playbook для использования этой role.

```
---
- name: Testing my module
  hosts: localhost
  gather_facts: false
  roles: 
    - my_role
```

**Шаг 12.** Заполните всю документацию по collection, выложите в свой репозиторий, поставьте тег `1.0.0` на этот коммит.

[Репозиторий с коллекцией](https://github.com/stepynin-georgy/my_own_collection/releases/tag/1.0.0)

**Шаг 13.** Создайте .tar.gz этой collection: `ansible-galaxy collection build` в корневой директории collection.

```
root@ansible-ubuntu:/opt/hw_ansible_6/ansible/my_own_namespace/yandex_cloud_elk# ansible-galaxy collection build
[WARNING]: You are running the development version of Ansible. You should only run Ansible from "devel" if you are modifying the Ansible engine, or trying out features
under development. This is a rapidly changing source of code and can become unstable at any point.
Created collection for my_own_namespace.yandex_cloud_elk at /opt/hw_ansible_6/ansible/my_own_namespace/yandex_cloud_elk/my_own_namespace-yandex_cloud_elk-1.0.0.tar.gz
root@ansible-ubuntu:/opt/hw_ansible_6/ansible/my_own_namespace/yandex_cloud_elk#
```

**Шаг 14.** Создайте ещё одну директорию любого наименования, перенесите туда single task playbook и архив c collection.

**Шаг 15.** Установите collection из локального архива: `ansible-galaxy collection install <archivename>.tar.gz`.

```
root@ansible-ubuntu:/home/user/test# ansible-galaxy collection install my_own_namespace-yandex_cloud_elk-1.0.0.tar.gz
[WARNING]: You are running the development version of Ansible. You should only run Ansible from "devel" if you are modifying the Ansible engine, or trying out features
under development. This is a rapidly changing source of code and can become unstable at any point.
Starting galaxy collection install process
Process install dependency map
Starting collection install process
Installing 'my_own_namespace.yandex_cloud_elk:1.0.0' to '/root/.ansible/collections/ansible_collections/my_own_namespace/yandex_cloud_elk'
my_own_namespace.yandex_cloud_elk:1.0.0 was installed successfully
root@ansible-ubuntu:/home/user/test#
```

**Шаг 16.** Запустите playbook, убедитесь, что он работает.

```
my_own_namespace-yandex_cloud_elk-1.0.0.tar.gz  single_task_playbook.yaml
root@ansible-ubuntu:/home/user/test# ansible-playbook single_task_playbook.yaml
[WARNING]: You are running the development version of Ansible. You should only run Ansible from "devel" if you are modifying the Ansible engine, or trying out features
under development. This is a rapidly changing source of code and can become unstable at any point.
[WARNING]: No inventory was parsed, only implicit localhost is available
[WARNING]: provided hosts list is empty, only localhost is available. Note that the implicit localhost does not match 'all'

PLAY [Test my module] ****************************************************************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************************************************************
ok: [localhost]

TASK [Create file] *******************************************************************************************************************************************************
changed: [localhost]

PLAY RECAP ***************************************************************************************************************************************************************
localhost                  : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

root@ansible-ubuntu:/home/user/test#

```

**Шаг 17.** В ответ необходимо прислать ссылки на collection и tar.gz архив, а также скриншоты выполнения пунктов 4, 6, 15 и 16.

## Необязательная часть

1. Реализуйте свой модуль для создания хостов в Yandex Cloud.
2. Модуль может и должен иметь зависимость от `yc`, основной функционал: создание ВМ с нужным сайзингом на основе нужной ОС. Дополнительные модули по созданию кластеров ClickHouse, MySQL и прочего реализовывать не надо, достаточно простейшего создания ВМ.
3. Модуль может формировать динамическое inventory, но эта часть не является обязательной, достаточно, чтобы он делал хосты с указанной спецификацией в YAML.
4. Протестируйте модуль на идемпотентность, исполнимость. При успехе добавьте этот модуль в свою коллекцию.
5. Измените playbook так, чтобы он умел создавать инфраструктуру под inventory, а после устанавливал весь ваш стек Observability на нужные хосты и настраивал его.
6. В итоге ваша коллекция обязательно должна содержать: clickhouse-role (если есть своя), lighthouse-role, vector-role, два модуля: my_own_module и модуль управления Yandex Cloud хостами и playbook, который демонстрирует создание Observability стека.

---

### Как оформить решение задания

Выполненное домашнее задание пришлите в виде ссылки на .md-файл в вашем репозитории.

---
