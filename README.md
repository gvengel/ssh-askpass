Installation
============

Copy or link the ssh-askpass script to /usr/local/bin. 

Install newt, so we can use whiptail CLI prompts.

    brew update && brew install newt

Make sure your shell is running its own ssh-agent. This prevents problems with rootless mode. Note the ssh-add at the end, and adjust accordingly.

_.bash_profile_

    # Setup our SSH agent
    export DISPLAY=:0
    export SSH_ASKPASS=/usr/local/bin/ssh-askpass

    connect_ssh_agent() {
        echo "$SSH_AUTH_SOCK" | grep com.apple.launchd > /dev/null && unset SSH_AUTH_SOCK && launchctl unload /System/Library/LaunchAgents/org.openbsd.ssh-agent.plist 2> /dev/null
        [ -e ~/.ssh/ssh-agent.info ] && . ~/.ssh/ssh-agent.info
        ssh-add -l > /dev/null 2>&1
        [ "$?" == "2" ] && ssh-agent -s | grep -v echo > ~/.ssh/ssh-agent.info && . ~/.ssh/ssh-agent.info
        ssh-add -l > /dev/null 2>&1
        [ "$?" == "1" ] && ssh-add -c -t 12h
    }

    connect_ssh_agent

Author
======

Gabe Van Engel <gabe@schizoid.net>
