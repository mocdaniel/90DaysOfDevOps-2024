---
theme: dracula
class: 'text-center'
highlighter: shiki
lineNumbers: false
drawings:
  persist: false
transition: slide-left
css: unocss
layout: two-cols
---

<div class="col">

  # From Automated to Automatic

  Event-Driven Infrastructure Management with Ansible

  <p class="text-gray pt-8">Daniel Bodky<br/>90 Days of DevOps 2024 | Day 27</p>

</div>


::right::

<div class="col items-center">

  ![Ansible Logo](/ansible_logo.svg)

</div>

<style>

  .col {
    @apply flex flex-col justify-center h-full;
  }

</style>

---
layout: default
---

# About Me

- Technology Evangelist at [NETWAYS Web Services](https://nws.netways.de)
- Big fan of **Automation** and **Ansible** specifically
- Always meddling with new tools and technologies

<style>

  ul {
    @apply pt-24;
  }

  li {
    @apply my-2 text-xl;
  }

</style>

---
layout: section
---

# What is Ansible?

---
layout: default
---

# What is Ansible?

<v-clicks at="0">

  - **Automation** tool for IT **services** and **configuration**
  - **Agentless** via SSH
  - Built on **Python** (logic) and **YAML** (configuration)
  - **Open Source** and easy to get started with

</v-clicks>

<style>

  ul {
    @apply pt-24;
  }

  p {
    @apply text-xl;
  }

</style>

---
layout: two-cols
---
# Inventories

- Ansible works with **inventories** of **hosts** and **groups**
- Default inventory is `/etc/ansible/hosts`
- Inventories can be defined using **YAML** or **INI** files
- Inventories can be **static** or **dynamic**
- Inventories can contain **custom variables**


::right::

<div class="code-col">

  ```yml
  # hosts.yml
  hosts:
    webservers:
      hosts:
        webshop.example.com:
          ansible_host: 192.168.1.10
          vars:
            webserver: apache2
        company.example.com:
          ansible_host: 192.168.1.11
            webserver: nginx
        internal.example.com:
          ansible_host: 192.168.1.12
            webserver: httpd
  ```

</div>

<style>

  ul {
    @apply pt-8;
  }

  .code-col {
    @apply h-full flex flex-col justify-center pl-14;
  }

  code {
    @apply text-sm;
  }

</style>

---
layout: two-cols
clicks: 5
---

# Ansible Ad-Hoc

<p class="pt-16 text-xl">

Ansible can be used to run **ad-hoc** commands on hosts:

</p>

<ul>

<li v-click>

  Specify the **host group** you want to modify

</li>
<li v-click>

  Specify the **inventory** file to use

</li>
<li v-click>

  Specify the **module** to run

</li>
<li v-click>

  Specify additional **arguments** for the module

</li>

</ul>


::right::

<div class="example-col">

```sh {all|2|3|4|5|all}{at:0}
ansible \
   webservers  \
  -i hosts.yml \
  -m package   \
  -a 'name="{{ webserver }}"'
```

</div>

<style>

  .example-col {
    @apply h-full flex flex-col justify-center pl-14;
  }

</style>

---
layout: two-cols
---

# Imperative vs. Declarative

<div class="col left">
  <v-click>

  > "Take two slices of bread. Slice a tomato. Fry the bacon. Wash the lettuce. Put it all together. Serve it to me."

  </v-click>
  <v-click>

  - Define exactly what you want to **happen**
  - Can be very **verbose**
  - **Error-prone** and hard to **reproduce**

  </v-click>

</div>

::right::

<div class="col right">
  <v-click>
  
  > "I'd like a BLT sandwich."

  </v-click>
  <v-click>
  
  - Define what you expect the **result** to be
  - **Concise** and **easy to read**
  - **Reproducible**

  </v-click>
</div>

<style>
  
  .col {
    @apply flex flex-col justify-between h-96 gap-4xl pt-4;
  }

  .right {
    @apply mt-14 ml-4;
  }

  .left {
    @apply mr-4;
  }

  blockquote {
    p {
      @apply text-2xl;
    }
  }

</style>

---
layout: two-cols
clicks: 5
---

# Tasks

<p class="pt-16 text-xl">

**Tasks** are the **building blocks** of declarative Ansible:

</p>

<ul>
  <li v-click>

  A **description** of the task's action isn't mandatory, but it's good practice
  
  </li>
  <li v-click>

  **Modules** define the action to be taken

  </li>
  <li v-click>

  **Arguments** can be passed to the module

  </li>
  <li v-click>

  Additional aspects of the task can be tweaked

  </li>
</ul>

::right::

<div class="code-col">

```yml {all|1|2|3-4|5-6|all}{at:0}
- name: Install webserver
  ansible.builtin.package:
    name: "{{ webserver }}"
    state: present
  become: true
  when: webserver is defined
```

</div>

<style>

  .code-col {
    @apply h-full flex flex-col justify-center pl-14;
  }

</style>

---
layout: default
---

# Roles and Collections

- **Roles** are a way to **organize** your Ansible code (Python and YAML)
- **Collections** are a way to **bundle** roles
- **Ansible Galaxy** is a repository for roles and collections

```sh
ansible-galaxy collection install kubernetes.core 
```

<style>

  ul {
    @apply pt-24 pb-16;
  }

  li {
    @apply my-2 text-xl;
  }

</style>

---
layout: default
clicks: 5
---

# Plays and Playbooks

<p class="pt-8 text-xl">

**Plays** are a collection of **tasks** and/or **roles** that are executed on a **group** of hosts:

</p>

<div class="flex flex-row gap-4 justify-around">

<ul>
  <li v-click>
  
  Plays take **names**, just like tasks
  
  </li>
  <li v-click>

  Plays should define a **host group** for scoped execution

  </li>
  <li v-click>

  Plays define (pre|post) **tasks** *and* **roles** to execute

  </li>
  <li v-click>

  **Variables** for the tasks and roles can be defined on the play level

  </li>
</ul>

```yml {all|1|2|6-12|3-5|all}{at:0}
- name: Install webservers
  hosts: webservers
  vars:
    webserver: apache2
  become: true
  tasks:
    - name: Install webserver
      ansible.builtin.package:
        name: "{{ webserver }}"
        state: present
  roles:
    - role: geerlingguy.mysql
```

</div>

<p v-click="5" class="text-xl">

A collection of **Plays** is called a **Playbook**

</p>

<style>
  code {
    @apply text-sm;
  }

</style>

---
layout: section
---

# Why Event-Driven?

---
layout: default
---

# In DevOps, Automation is Key

<div class="facts">
<p v-click>

  ✅ **Tests** are run automatically
</p>
<p v-click>

  ✅ **Builds** are run automatically</p>
<p v-click>

  ✅ **Deployments** are run automatically
</p>
<p v-click>

  ✅ **Infrastructure** is provisioned automatically
</p>
<br />
<p v-click>

  ❌ **Maintenance** and **Incident Response** are (mostly) manual
</p>
</div>

<style>

  .facts {
    @apply pt-24;
  }

  p {
    @apply my-2 text-xl;
  }

</style>

---
layout: default
---

# Automation for Operations

<p class="p">

  Many **operations** tasks are **repetitive** and **predictable**:
</p>

<ul>
  <li v-click>

  **Pruning** old backups
  </li>
  <li v-click>

  **Restarting** services upon failure
  </li>
  <li v-click>

  **Scaling** infrastructure, storage, etc.
  </li>
</ul>

<p class="p" v-click>

  🛑 With **repetition** comes **boredom** and **human error**
</p>

<style>

  .p {
    @apply pt-16 text-xl;
  }

  li {
    @apply my-2 text-xl;
  }

</style>

---

# Event-Driven Ansible

- Many tasks can be easily **automated** with Ansible

- Triggering these tasks upon external **events** makes the automation **automatic**

- Event Driven Ansible (**EDA**) implements this concept

<style>

  ul {
    @apply pt-24 pb-16;
  }

  li {
    @apply my-2 text-xl;
  }

</style>

---

# EDA Explained

<img class="pt-16 mx-auto w-128" src="/eda-overview.png" alt="An overview over the EDA architecture" />