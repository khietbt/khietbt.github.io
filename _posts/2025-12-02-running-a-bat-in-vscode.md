---
layout: post
title: Running a .BAT in VSCode
date: 2025-12-02T10:06:00.000+07:00
image: https://www.jamsscheduler.com/wp-content/uploads/2021/06/jm-whats-in-your-bat-file-blog-1200x400-1.png
categories:
  - vscode
tags:
  - vscode
  - batch
  - launch.json
---
[keycloak](https://www.keycloak.org/) is a great open-source identity management system. In Windows, if you cannot use docker to run a local instance, please try to add an entry to VSCode's launch.json like below:

```json
{
  "type": "node-terminal",
  "name": "KeycloakService",
  "request": "launch",
  "command": "'${KEYCLOAK_ROOT_DIR}\\bin\\kc.bat' 'start'",
  "envFile": "${KEYCLOAK_ROOT_DIR}\\.env"
}
```
