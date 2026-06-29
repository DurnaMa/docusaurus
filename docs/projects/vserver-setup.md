# V-Server Setup

<!--INSERT YOUR BRIEF DESCRIPTION HERE -->
This page documents how I configured my very first cloud server instance in the Developer Akademie DevSecOps Course.

## TOC

<!--INSERT YOUR TABLE OF CONTENTS HERE -->

import GithubLinkAdmonition from '@site/src/components/GithubLinkAdmonition';

<GithubLinkAdmonition 
    link="https://github.com/spmse/dev-blog-template"
    title="Github Tip" 
    type="tip"
/>

## Quickstart

1. do X
2. do Y

## Description

this is an *example* of a **description**.

## Further References

### Meins

# Your first Cloud-VM

## Diesable Password.Logins

In this section information about disabling password based logins will be provided.
Passwirds cann be a source of error potential which is why logins should be disabled in favor of authenticating via SSH-Keys.

1. Adjust the configuration under `/etc/ssh/sshd_config`
2. Find and edit the line `#PasswordAuthentication yes` to that `PasswordAuthentication no`.
3. Save the file and exit
4. Restart the `sshd` sevice to reload the config changes