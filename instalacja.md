# Dokumentacja instalacji oraz użytkowania Ansible
## 1. Instalacja

_sudo yum update -y_

_sudo dnf install ansible_

_ansible --version_
## 2. Konfiguracja
>Utworzenie listy hostów
```
[moje_serwery]

192.168.1.2 ansible_user=admin
```
## 3. Ping

_ansible -i hosty moje_serwery -m ping_

```
[WARNING]: Host '192.168.1.60' is using the discovered Python interpreter at '/usr/bin/python3.13', but future installation of another Python interpreter could cause a different interpreter to be discovered. See https://docs.ansible.com/ansible-core/2.20/reference_appendices/interpreter_discovery.html for more information.
192.168.1.60 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3.13"
    },
    "changed": false,
    "ping": "pong"
}
```
> Ping powiódł się, ale ansible nie wie skąd czerpać ścieżkę do pythona
```
[moje_serwery]
192.168.1.2 ansible_user=admin ansible_python_interpreter=/usr/bin/python3.13
```
## 4. Tworzenie playbooka
>stworzenie test.yml
```
---
- name: Tescik
hosts: moj_serwer
tasks:
- name: test polaczenia
    ping:
```
## 5. Uruchomienie playbooka
ansible-playbook -i hosty test.yml
```
PLAY [Tescik] ******************************************************************************************************************

TASK [Gathering Facts] *********************************************************************************************************
ok: [192.168.1.2]

TASK [test polaczenia] *********************************************************************************************************
ok: [192.168.1.2]

PLAY RECAP *********************************************************************************************************************
192.168.1.2                : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```
