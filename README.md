# **dotfiles**

1. [File Descriptions](#file-descriptions)
2. [Execution](#execution)
    - [Encrypting Keys](#encrypting-keys)
    - [macOS](#macos)
    - [Linux](#linux)
    
---
     
## **Execution:**

![execution](https://github.com/msanchariii/dotfiles/assets/113378204/5163ad49-277b-49c8-abda-7bb8b3d650c7)
     
### **`Encrypting Keys`**

Store a single key in a file `your_text_file` & encrypt like this for all the keys

```
ansible-vault encrypt your_text_file
```

Create a variable for the encrypted or plain text key and store it in `credentials.yml`.
    
---
    
## **`macOS`**

**Install Ansible & Homebrew:**

```
bash -c "$(curl -fsSL https://raw.githubusercontent.com/suyashbhawsar/dotfiles/main/macOS-setup.sh)"
```

**Note: Quit & re-open the Terminal**

**Set up git:**

```
ansible-pull -U https://github.com/suyashbhawsar/dotfiles --vault-id @prompt --tags mac-minimal,mac-full git.yml
```

**Install 'Minimal' Packages & Configurations:**

```
ansible-pull -U https://github.com/suyashbhawsar/dotfiles --tags mac-minimal install.yml
```

**Install 'Full' Packages & Configurations:**

```
ansible-pull -U https://github.com/suyashbhawsar/dotfiles --tags mac-full install.yml
```

**Remove Packages & Configurations:**

```
ansible-pull -U https://github.com/suyashbhawsar/dotfiles --tags mac remove.yml
```

---

## **`Linux`**

**Clone the repo & build the docker image**

```
cd ~/ && rm -rf dotfiles && git clone https://github.com/suyashbhawsar/dotfiles.git && docker stop $(docker ps -a | grep "debian-ansible" | sed 's/\|/ /'|awk '{print $1}') | xargs docker rm && docker rmi debian-ansible && docker build -t debian-ansible .
```

**Start a container from the docker image**

```
docker run -it --rm debian-ansible /bin/bash
```

**Set up git:**

```
ansible-pull -U https://github.com/suyashbhawsar/dotfiles --vault-id @prompt --tags linux git.yml
```

**Install Packages & Configurations:**

```
ansible-pull -U https://github.com/suyashbhawsar/dotfiles --tags linux install.yml
```

**Remove Packages & Configurations:**

```
ansible-pull -U https://github.com/suyashbhawsar/dotfiles --tags linux remove.yml
```

---

## **File Descriptions:**

- `Dockerfile`: Defines the Docker image with all necessary dependencies for running Ansible on a Debian-based system.
- `macOS-setup.sh`: Shell script to install Homebrew, Python, and Ansible on macOS.
- `credentials.yml`: Stores variables for both plain text and encrypted credentials, used within the playbooks.
- `personal_git.yml` & `work_git.yml`: This Ansible playbook is designed to configure Git settings, manage SSH keys, and clone a specific Git repository. Below is a detailed explanation of each section and task in the playbook.
    - Playbook Overview
        - Variable Files:
            - credentials.yml (This file contains sensitive information such as your personal Git username, email, and SSH keys.)
        - Tasks Breakdown:
            1. **Git Configuration**
                
                **Task 1:** Set Git user.name
                
                ```yaml
                - name: "Git | Set user.name"
                community.general.git_config:
                    name: user.name
                    scope: global
                    value: "{{ personal_git_username }}"
                become: false
                ```
                
                - **Purpose**: Configures the global Git username.
                - **Details**:
                    - **Module**: `community.general.git_config`
                    - **Parameter `name`**: Sets the Git configuration variable `user.name`.
                    - **Parameter `scope`**: `global` indicates the setting is applied globally.
                    - **Parameter `value`**: Uses the variable `personal_git_username` (personal_git.yml) or `work_git_username` (work_git.yml) from `credentials.yml`.
                    - **Parameter `become`**: `false` ensures that the task does not require elevated privileges.


                **Task 2:** Set Git user.email
    
                ```yaml
                    - name: "Git | Set user.email"
                    community.general.git_config:
                        name: user.email
                        scope: global
                        value: "{{ personal_git_email }}"
                    become: false
                ```
    
                - **Purpose**: Configures the global Git email.
                - **Details**:
                    - **Module**: `community.general.git_config`
                    - **Parameter `name`**: Sets the Git configuration variable `user.email`.
                    - **Parameter `scope`**: `global` indicates the setting is applied globally.
                    - **Parameter `value`**: Uses the variable `personal_git_email` (personal_git.yml) or `work_git_email` (work_git.yml) from `credentials.yml`.
                    - **Parameter `become`**: `false` ensures that the task does not require elevated privileges.
         
                   
            2. **SSH Key Management**
     
                           
                **Task 3:** Create `.ssh` Directory
                    
                ```yaml
                - name: Create .ssh directory
                file:
                    path: "{{ ansible_user_dir }}/.ssh"
                    state: directory
                    mode: '0700'
                become: false
                ```
    
                - **Purpose**: Ensures the existence of the `.ssh` directory with the correct permissions.
                - **Details**:
                    - **Module**: `file`
                    - **Parameter `path`**: Creates the directory at the path `{{ ansible_user_dir }}/.ssh`.
                    - **Parameter `state`**: `directory` ensures the path is a directory.
                    - **Parameter `mode`**: Sets permissions to `0700` (read, write, execute for owner only).
                    - **Parameter `become`**: `false` ensures that the task does not require elevated privileges.
    
                **Task 4:** Copy Public SSH Key
    
                ```yaml
                - name: "SSH | Copy personal_rsa Public SSH key"
                copy:
                    dest: "{{ ansible_user_dir }}/.ssh/personal_rsa.pub"
                    content: "{{ personal_ssh_key_public }}"
                    mode: '0644'
                become: false
                ```
    
                - **Purpose**: Ensures the existence of the `.ssh` directory with the correct permissions.
                - **Details**:
                    - **Module**: `file`
                    - **Parameter `path`**: Creates the directory at the path `{{ ansible_user_dir }}/.ssh`.
                    - **Parameter `state`**: `directory` ensures the path is a directory.
                    - **Parameter `mode`**: Sets permissions to `0700` (read, write, execute for owner only).
                    - **Parameter `become`**: `false` ensures that the task does not require elevated privileges.

- `install.yml`: Ansible playbook that runs the post-stow configuration (from the private repository: .dotfiles) after installing and configuring packages.
- `remove.yml`: Playbook to remove installed packages and configurations.
