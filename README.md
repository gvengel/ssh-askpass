Installation
============

Copy or link the ssh-askpass script to /usr/local/bin. 

Install newt, so we can use whiptail CLI prompts. (Example uses [Homebrew](http://brew.sh/) for installation)

    brew update && brew install newt

By default, macOS looks for ssh-askpass in /usr/libexec; however, this location is protected by rootless mode. Add the following to your `.bash_profile`, which redirects ssh-agent to look in /usr/local/bin. Note the call to ssh-add, if you wish to adjust the timeout.

_.bash_profile_

    # Setup our SSH agent
    connect_ssh_agent() {
        export SSH_AUTH_SOCK=$(ls /private/tmp/com.apple.launchd.*/Listeners)
        ssh-add -l > /dev/null 2>&1
        if [ "$?" != "0" ]; then
            launchctl setenv SSH_ASKPASS /usr/local/bin/ssh-askpass DISPLAY :0 && launchctl stop com.openssh.ssh-agent
            ssh-add -c -t 12h
        fi
    }
    connect_ssh_agent

You may also wish to automatically start tmux when connecting over ssh. First add `new-session` to your `.tmux.conf`, then add the following to your `.bash_profile`.

_.bash_profile_

    if [ "$SSH_CONNECTION" -a -z "$TMUX" ]; then
        tmux attach -d
    fi
