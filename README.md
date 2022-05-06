# tsm_client_ansible
Ansible for install TSM client and configure

This is for newly added TSM client.
The playbook works for 
1. create tsminst1 user and group 
2. wget tsm 
3. install tsm client 
4. configuration(copy dsm.sys, dsm.opt)


# Inventory
[asia] for ping test the client.

[TSMclient] you can add multiple TSM clients.


# Tasks
* mkuser
```bash
---
- name: create tsminst1 group
  group:
    name: "{{ item.grp_name }}"
    gid: "{{ item.gid }}"
    state: present
  with_items: "{{ group_list }}"

- name: mkuser tsminst1
  user:
    name: "{{ item.id }}"
    update_password: always
    uid: "{{ item.uid }}"
    groups: "{{ item.groups }}"
    password: "{{ item.new_password | password_hash('sha512') }}"
  with_items: "{{ user_list }}"

```
* wget TSM
```bash
---
- name: TSM wget file
  file:
    path: "{{ istall_dir }}"
    state: directory
    mode: '0744'

- name: Download
  get_url: 
    url="{{ url }}" 
    dest="{{ dest_dir }}" 
    mode='0744' 

- name: TAR XVF
  unarchive:
    src: '/opt/tivoli/tsm/tsm_install/TSMinstall_file.tar'
    dest: '{{ dir }}'
    remote_src: yes
    owner: 'root'
    mode: '0744'
```

* TSM install (You may change the versions)
```bash
---
- name: install lsof
  yum:
    state: present
    name:
      -  lsof

- name: install gskcrypt64 rpm
  yum:
    name: /opt/tivoli/tsm/tsm_install/gskcrypt64-8.0.55.9.linux.x86_64.rpm
    state: present

- name: install gskssl64 rpm
  yum:
    name: /opt/tivoli/tsm/tsm_install/gskssl64-8.0.55.9.linux.x86_64.rpm
    state: present

- name: install TIVsm-API64 rpm
  yum:
    name: /opt/tivoli/tsm/tsm_install/TIVsm-API64.x86_64.rpm
    state: present

- name: install TIVsm-APIcit rpm
  yum:
    name: /opt/tivoli/tsm/tsm_install/TIVsm-APIcit.x86_64.rpm
    state: present

- name: install TIVsm-BA rpm
  yum:
    name: /opt/tivoli/tsm/tsm_install/TIVsm-BA.x86_64.rpm
    state: present

- name: install TIVsm-BAcit rpm
  yum:
    name: /opt/tivoli/tsm/tsm_install/TIVsm-BAcit.x86_64.rpm
    state: present

- name: install TIVsm-BAhdw rpm
  yum:
    name: /opt/tivoli/tsm/tsm_install/TIVsm-BAhdw.x86_64.rpm
    state: present

- name: install TIVsm-WEBGUI rpm
  yum:
    name: /opt/tivoli/tsm/tsm_install/TIVsm-WEBGUI.x86_64.rpm
    state: present
```

* TSM Conf(dsm.sys / dsm.opt)
```bash
---
- name: Copy dsm.sys
  copy:
    src: /opt/tivoli/tsm/client/ba/bin/dsm.sys.smp
    dest: /opt/tivoli/tsm/client/ba/bin/dsm.sys
    remote_src: yes

- name: Copy dsm.opt
  copy:
    src: /opt/tivoli/tsm/client/ba/bin/dsm.opt.smp
    dest: /opt/tivoli/tsm/client/ba/bin/dsm.opt
    remote_src: yes
```



# Playbook

```bash
---
- name: TSM client
  hosts: TSMclient
  roles:
  -  role: mkuser
  -  role: tsm_wget
  -  role: tsm_install
  -  role: tsm_conf

