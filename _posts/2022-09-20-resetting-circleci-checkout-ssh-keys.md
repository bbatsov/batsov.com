---
title: Resetting CircleCI Checkout SSH Keys
date: 2022-09-20 09:22 +0300
tags:
- CircleCI
- GitHub
- Tutorial
---

Lately I've been having some weird problems with CircleCI and some of my OSS
projects (most recently CIDER) - the SSH checkout keys that CircleCI uses to
fetch the code from GitHub started to disappear which resulted in the following
obscure error messages:

```
Using SSH Config Dir '/root/.ssh'
git version 2.30.2
Cloning git repository
Cloning into '.'...
Warning: Permanently added the ECDSA host key for IP address '140.82.112.4' to the list of known hosts.
git@github.com: Permission denied (publickey).
fatal: Could not read from remote repository.

Please make sure you have the correct access rights
and the repository exists.
```

At first I was really puzzled what has happened and I thought it might be some CircleCI issue, but after some
digging I've noticed that the CircleCI deploy key has disappeared from the GitHub project's settings (that's under "Settings -> Deploy keys").

There are two simple ways to regenerate the missing key:

1. Go to CircleCI's console and disable building the project in question (basically press "Stop building"). Once you re-enable the project the SSH key will
be regenerated and added to GitHub.
2. Alternatively you can go to your project settings in CircleCI's console, delete the read checkout SSH key there and re-add it. That's under "Project settings -> SSH keys" and should be the first key you see there.

I think that option 2) is the better one, simply because it takes a bit less time, but both get the job done. Still, it remains a mystery to me what caused this and more
of my repos get affected by it. Maybe the SSH keys that disappeared were revoked in relation to [this security aleart](https://discuss.circleci.com/t/circleci-security-alert-warning-phishing-attempt-for-login-credentials/45408)?

That's all I have for you today. Hopefully this article will save you some time if you encounter the same problem.
