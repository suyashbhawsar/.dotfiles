# **dotfiles**

1. [Execution](#execution)
    - [Encrypting Keys](#encrypting-keys)
    - [macOS](#macos)
    - [Linux](#linux)
2. [File Descriptions](#file-descriptions)
    - [Dockerfile](#dockerfile)
    - [macOS-setup.sh](#macos-setupsh)
    - [credentials.yml](#credentialsyml)
    - [personal_git.yml & work_git.yml](#personal_gityml--work_gityml)
    - [install.yml](#installyml)
    - [remove.yml](#removeyml)



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

### `Dockerfile`:
Defines the Docker image with all necessary dependencies for running Ansible on a Debian-based system.
___

### `macOS-setup.sh`:
This `macOS-setup.sh` script is designed to automate the installation of essential development tools on macOS. It checks for the presence of Homebrew, Python3, Ansible, and Stow, and installs them if they are not already installed. The script ensures that your development environment is set up correctly and efficiently.


- Script Overview:
    - **File Name**: `macOS-setup.sh`
    - **Purpose**: Automates the installation of Homebrew, Python3, Ansible, and Stow on macOS.
- Script Breakdown:
    1. Checking and Installing Homebrew

        ```bash
        if ! command -v brew >/dev/null 2>&1; then
          echo -e "\033[1;34mInstalling Homebrew\033[0m"
          /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
          echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zprofile
          eval "$(/opt/homebrew/bin/brew shellenv)"
        else
          echo -e "\033[1;34mHomebrew is already installed\033[0m"
        fi
        ```

        - **Purpose**: Checks if Homebrew is installed and installs it if not.
        - **Details**:
            - Check: `command -v brew` checks if the `brew` command is available.
            - **Installation**: If Homebrew is not found, it is installed using the official installation script from Homebrew's GitHub repository.
            - **Environment Setup**: Adds Homebrew to the shell environment by appending `eval "$(/opt/homebrew/bin/brew shellenv)"` to `~/.zprofile` and then evaluates the command.
            
    2. Checking and Installing Python3

        ```bash
        if ! command -v python3 >/dev/null 2>&1; then
          echo -e "\033[1;34mInstalling Python\033[0m"
          brew install python
        else
          echo -e "\033[1;34mPython is already installed\033[0m"
        fi
        ```

        - **Purpose**: Checks if Python3 is installed and installs it if not.
        - **Details**:
            - Check: `command -v python3` checks if the `python3` command is available.
            - **Installation**: If Python3 is not found, it is installed using Homebrew with the command `brew install python`.

    3. Checking and Installing Ansible

        ```bash
        if ! command -v ansible >/dev/null 2>&1; then
          echo -e "\033[1;34mInstalling Ansible\033[0m"
          brew install ansible
        else
          echo -e "\033[1;34mAnsible is already installed\033[0m"
        fi
        ```

        - **Purpose**: Checks if Ansible is installed and installs it if not.
        - **Details**:
            - Check: `command -v ansible` checks if the `ansible` command is available.
            - **Installation**: If Ansible is not found, it is installed using Homebrew with the command `brew install ansible`.

    4. Checking and Installing Stow

        ```bash
        if ! command -v stow >/dev/null 2>&1; then
          echo -e "\033[1;34mInstalling Stow\033[0m"
          brew install stow
        else
          echo -e "\033[1;34mStow is already installed\033[0m"
        fi
        ```

        - **Purpose**: Checks if Stow is installed and installs it if not.
        - **Details**:
            - Check: `command -v stow` checks if the `stow` command is available.
            - **Installation**: If Stow is not found, it is installed using Homebrew with the command `brew install stow`.

___

### `credentials.yml`:
The `credentials.yml` file is an Ansible variable file used to securely store sensitive information such as Git credentials and SSH keys. This file utilizes Ansible Vault to encrypt sensitive data, ensuring that it remains secure. Below is a detailed explanation of the contents and structure of the `credentials.yml` file.

- Script Overview:
    - **File Name**: `credentials.yml`
    - **Purpose**: Stores sensitive personal and work-related credentials securely using Ansible Vault.
- Variables Breakdown:
    1. `personal_git_username`

        ```yaml
        personal_git_username: "suyashbhawsar"
        ```

        - **Purpose**: Placeholder for personal Git username.
        - **Details**:
            - **Variable Name**: `personal_git_username`
            - **Value**: `"suyashbhawsar"` (Your GitHub Personal Account username)
    
    2. `personal_git_email`

        ```yaml
        personal_git_email: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          33346234333739633337643138353332646438646539616138623136376665633937623934346566
          3933366634383537336536363632653661303363373364380a653537366431643161633236613764
          35373037313433373638613961383632656634316530633231636633636663313330333364633634
          3434626335323037300a313530333937653866663461663137663530363330336538326466353262
          37386434363661303766366365633235316363613738333031396630323031343933
        ```

        - **Purpose**: Placeholder for encrypted personal Git email address.
        - **Details**:
            - **Variable Name**: `personal_git_email`
            - **Value**: Encrypted string (Email Address) using Ansible Vault
    
    3. `personal_ssh_key_public`

        ```yaml
        personal_ssh_key_public: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          63643130346438346133376336613730356562616464373633636361656633663265616538313466
          3763336434343962623138343331326333333061663433350a306261623037343063396135636635
          ....
        ```

        - **Purpose**: Placeholder for encrypted public SSH key.
        - **Details**:
            - **Variable Name**: `personal_ssh_key_public`
            - **Value**: Encrypted string (Public SSH Key) using Ansible Vault
    
    4. `personal_ssh_key_private`

        ```yaml
        personal_ssh_key_private: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          36343535663139613931343264303465623830653539386133653236626363323839643963346365
          6339353936343531636262326437396263643261636435330a663734353937363533306335666164
          ....
        ```

        - **Purpose**: Placeholder for encrypted private SSH key.
        - **Details**:
            - **Variable Name**: `personal_ssh_key_private`
            - **Value**: Encrypted string (Private SSH Key) using Ansible Vault

___

### `personal_git.yml` & `work_git.yml`:
These Ansible playbooks are designed to configure Git settings, manage SSH keys, and clone `.dotfiles` Git repository. Below is a detailed explanation of each section and task in the playbook:

- Playbook Overview:
    - Variable Files:
        - `credentials.yml` (This file contains sensitive information such as your personal Git username, email, and SSH keys.)
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

            - **Purpose**: Copies the public SSH key to the `.ssh` directory.
            - **Details**:
                - **Module**: `copy`
                - **Parameter `dest`**: Destination path for the public key.
                - **Parameter `content`**: Uses the variable `personal_ssh_key_public` (personal_git.yml) or `work_ssh_key_public` (work_git.yml) from `credentials.yml`.
                - **Parameter `mode`**: Sets permissions to `0644` (read and write for owner, read for others).
                - **Parameter `become`**: `false` ensures that the task does not require elevated privileges.

            **Task 5:** Copy Private SSH Key

            ```yaml
            - name: "SSH | Copy personal_rsa Private SSH key"
              copy:
                dest: "{{ ansible_user_dir }}/.ssh/personal_rsa"
                content: "{{ personal_ssh_key_private }}"
                mode: '0600'
              become: false
            ```

            - **Purpose**: Copies the private SSH key to the `.ssh` directory.
            - **Details**:
                - **Module**: `copy`
                - **Parameter `dest`**: Destination path for the private key.
                - **Parameter `content`**: Uses the variable `personal_ssh_key_private` (personal_git.yml) or `work_ssh_key_private` (work_git.yml) from `credentials.yml`.
                - **Parameter `mode`**: Sets permissions to `0600` (read and write for owner only).
                - **Parameter `become`**: `false` ensures that the task does not require elevated privileges.

        3. **Repository Management (Only in `personal_git.yml`)**

            **Task 6:** Remove Existing Repository Directory

            ```yaml
            - name: Remove repository directory
              file:
                path: ~/.dotfiles
                state: absent
              become: false
            ```

            - **Purpose**: Ensures the removal of any existing `.dotfiles` directory to avoid conflicts.
            - **Details**:
                - **Module**: `file`
                - **Parameter `path`**: Path to the `.dotfiles` directory.
                - **Parameter `state`**: `absent` ensures the directory is removed if it exists.
                - **Parameter `become`**: `false` ensures that the task does not require elevated privileges.

            **Task 7:** Clone Repository

            ```yaml
            - name: Clone repository
              shell: GIT_SSH_COMMAND="ssh -i ~/.ssh/temp" git clone git@github.com:{{ personal_git_username }}/.dotfiles.git ~/.dotfiles
              become: false
            ```

            - **Purpose**: Clones the specified Git repository into the `.dotfiles` directory.
            - **Details**:
                - **Module**: `shell`
                - **Command**: Uses a custom SSH command to clone the repository from GitHub.
                - **Parameter `GIT_SSH_COMMAND`**: Specifies the SSH command with the appropriate private key.
                - **Repository URL**: `git@github.com:{{ personal_git_username }}/.dotfiles.git`
                - **Destination**: Clones into the `~/.dotfiles` directory.
                - **Parameter `become`**: `false` ensures that the task does not require elevated privileges.

___

### `install.yml`:
Ansible playbook that runs the post-stow configuration (from the private repository: .dotfiles) after installing and configuring packages.
#### `install.yml`:

The `install.yml` file is a part of the Ansible setup and is responsible for running post-configuration tasks after the initial git configuration. This file imports another playbook located in the user's home directory under `.dotfiles`.

- File Overview:
    - **File Name**: `install.yml`
    - **Purpose**: Runs post-configuration tasks after git configuration by importing another playbook.
- Variables Breakdown:
    1. Running Post-Git Configuration

        ```yaml
        - name: Run post-git configuration
          import_playbook: "{{ lookup('env', 'HOME') }}/.dotfiles/post_git.yml"
        ```

        - **Purpose**: Imports and runs the `post_git.yml` playbook located in the user's `.dotfiles` directory.
        - **Details**:
            - **Task Name**: Run post-git configuration
            - **Module**: `import_playbook`
            - **Parameter `import_playbook`**: Specifies the path to the `post_git.yml` playbook.
                - **Path**: The path is dynamically set using `lookup('env', 'HOME')` to get the user's home directory and appending `.dotfiles/post_git.yml`.

___

### `remove.yml`:
Playbook to remove installed packages and configurations.
