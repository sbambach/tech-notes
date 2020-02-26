# SSH-Agent support for vagrant+Virtualbox on windows (git-bash)

## Install Git for windows (git-bash)

[Download Git for windows](https://github.com/git-for-windows/git/releases/latest)

## Program versions used

- Windows 10 Pro (1909)
- VirtualBox 6.0.16
- vagrant 2.2.7
- git-bash 2.25.1

## Activate ssh-agent forwarding within ssh client config

1) Open `git-bash` console and create ssh dir: `mkdir ~/.ssh`
2) Change permissions for ssh dir: `chmod 700 ~/.ssh`
3) Create/Add options to your ssh config: *~/.ssh/config*

    ```txt
    Host *
        ForwardAgent yes
        AddKeysToAgent yes
    ```

## Install your SSH keys (if you already have them)

In case you already have private and public ssh-keys (not from putty!), copy them to **~/.ssh/** directory.

## Otherwise generate new SSH keys

1) Open `git-bash` console
2) Generate key with: `ssh-keygen -t rsa -b 4096`
3) **Provide a password, when asked!**
4) Your keys are now stored in
   - *~/.ssh/id_rsa* (private key)
   - *~/.ssh/id_rsa.pub* (public key)

## Ensure that ssh-agent is started and your ssh-key is added to agent

1) Create/Add block to your *~/.profile*

    ```bash
    # .profile
    # based on https://gist.github.com/bsara/5c4d90db3016814a3d2fe38d314f9c23

    SSH_ENV="$HOME/.ssh/agent_git-bash.env"
    SSH_KEYFILE="$HOME/.ssh/id_rsa"

    function run_ssh_env {
      . "${SSH_ENV}" > /dev/null
    }

    function start_ssh_agent {
      if [ -r "${SSH_KEYFILE}" ]; then
        echo "Initializing new SSH agent..."
        ssh-agent | sed 's/^echo/#echo/' > "${SSH_ENV}"
        echo "succeeded"
        chmod 600 "${SSH_ENV}"
        run_ssh_env
        ssh-add "${SSH_KEYFILE}"
      else
        echo "ERROR: SSH-Key file \"${SSH_KEYFILE}\" does not exist!"
        echo "  You need to install your existing key, or generate a new one:"
        echo "  ssh-keygen -t rsa -b 4096 -f \"${SSH_KEYFILE}\""
        echo ""
        echo "INFO:  Afterwards close git-bash windows, and reopen it."
      fi
    }

    if [ -f "${SSH_ENV}" ]; then
      run_ssh_env
      ps -ef | grep ${SSH_AGENT_PID} | grep ssh-agent$ > /dev/null || {
        start_ssh_agent
      }
    else
      start_ssh_agent
    fi
    ```

2) Close *git-bash* window
3) Reopen `git-bash` window
4) You should be asked for your SSH key password for the first time.
5) Test if your key is loaded by agent with: `ssh-add -l`

## Test if you can login to GIT

1) Open `git-bash` console
2) For that easy test to work, you need a github account setup with SSH-key access!  
   [See official docu on github](https://help.github.com/en/github/authenticating-to-github/testing-your-ssh-connection).
3) Try git access with ssh
    ```bash
    $ ssh -T git@github.com
    Hi sbambach! You've successfully authenticated, but GitHub does not provide shell access.
    ```
4) Try git access with git
    ```bash
    $ git ls-remote ssh://git@github.com/sbambach/tech-notes.git
    f080ca08b4fad1b38cdcff29e8d49a55a7c9994d        HEAD
    f080ca08b4fad1b38cdcff29e8d49a55a7c9994d        refs/heads/master
    ```
5) Both commands should **not** ask for password!
