Installation
============

Copy or link the ssh-askpass script to /usr/local/bin. Install newt, terminal-notifier, and reattach-to-user-namespace packages.

    brew update && brew install newt terminal-notifier reattach-to-user-namespace

Enable reattach-to-user-namespace in your .tmux.conf.

    set -g default-command "which reattach-to-user-namespace > /dev/null && reattach-to-user-namespace -l $SHELL || $SHELL -l"
    
Make sure your shell is running it's own ssh-agent. This prevents problems with rootless mode. Note the ssh-add at the end, and adjust accordingly.

_.bash_profile_

    connect_ssh_agent() {
        echo "$SSH_AUTH_SOCK" | grep com.apple.launchd > /dev/null && unset SSH_AUTH_SOCK && launchctl unload /System/Library/LaunchAgents/org.openbsd.ssh-agent.plist 2> /dev/null
        [ -e ~/.ssh/ssh-agent.info ] && . ~/.ssh/ssh-agent.info
        ssh-add -l > /dev/null 2>&1
        [ "$?" == "2" ] && SSH_ASKPASS=/usr/local/bin/ssh-askpass ssh-agent -s | grep -v echo > ~/.ssh/ssh-agent.info && . ~/.ssh/ssh-agent.info
        ssh-add -l > /dev/null 2>&1
        [ "$?" == "1" ] && ssh-add -c -t 12h
    }
    connect_ssh_agent

Author
======

Gabe Van Engel <gabe@schizoid.net>
