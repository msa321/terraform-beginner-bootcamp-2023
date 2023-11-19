# Terraform Beginner Bootcamp 2023

## Semantic Versioning :mage:

This project is going utlize semantic versioning for its tagging.
[semver.org](https://semver.org/)

The general format:
**MAJOR.MINOR.PATCH**, eg. `1.0.1`


**MAJOR** version when you make incompatible API changes
**MINOR** version when you add functionality in a backward compatible manner
**PATCH** version when you make backward compatible bug fixes

## Install the Terraform CLI

### Considerations with the Terraform CLI changes
The Terraform CLI installation instructions have changed due to gpg keyring changes. So we needed refer to the latest install CLI instructions via Terraform Documentation and change the scripting for install.

[Install Terrafrom CLI](https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli)

### Considerations for Linux Distribution

This projecty is built against Ubuntu
Please consider checking your Linux Distribution and change accordingly to distribution needs: [Checking OS Version](https://www.cyberciti.biz/faq/how-to-check-os-version-in-linux-command-line/)

#### Input
```
cat /etc/os-release
```

#### Output
```sh
PRETTY_NAME="Ubuntu 22.04.3 LTS"
NAME="Ubuntu"
VERSION_ID="22.04"
VERSION="22.04.3 LTS (Jammy Jellyfish)"
VERSION_CODENAME=jammy
ID=ubuntu
ID_LIKE=debian
HOME_URL="https://www.ubuntu.com/"
SUPPORT_URL="https://help.ubuntu.com/"
BUG_REPORT_URL="https://bugs.launchpad.net/ubuntu/"
PRIVACY_POLICY_URL="https://www.ubuntu.com/legal/terms-and-policies/privacy-policy"
UBUNTU_CODENAME=jammy
```

### Refactoring into Bash Scripts

While fixing the Terraform CLI gpg depreciation issues we notice that bash scripts steps were a considerable amount more code. So we decided to create a bash script to install the Terraform CLI.

This bash script is located here: ([./bin/install_terraform_cli](./bin/install_terraform_cli))

- This will keep the Gitpod Task File ([.gitpod.yaml](.gitpod.yml)) tidy 
- This allow us an easier to debug and execute manually Teraaform CLI install
- This will allow better portability for other projects that need to install Terraform CLI



## Making Executable Files: She-Bang

A Shebang  (prounced Sha-bang) tells the bash script what program that will interpet the script. Eg. `#!/bin/bash`

#### Execution Permissions
When executing the bash script we can use the `./` shorthand notation to execute the bash script.

An easy and effiecent method to quickly launch multiple script of codes from a file and can be very useful when automating too: [Excutable Files](https://en.wikipedia.org/wiki/Shebang_(Unix))

#### File Content

```bash
#!/usr/bin/env bash

sudo apt-get update && sudo apt-get install -y gnupg software-properties-common curl

wget -O- https://apt.releases.hashicorp.com/gpg | 
gpg --dearmor | \
sudo tee /usr/share/keyrings/hashicorp-archive-keyring.gpg

gpg --no-default-keyring \
--keyring /usr/share/keyrings/hashicorp-archive-keyring.gpg \
--fingerprint

echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] \
https://apt.releases.hashicorp.com $(lsb_release -cs) main" | \
sudo tee /etc/apt/sources.list.d/hashicorp.list

sudo apt update

sudo apt-get install terraform -y

```


## Linux Permissions Considerations: 

The executable file we created couldn't be executed straight away just because the file permission within the directory had to be changed. 

As when we try to launch the file by excuting: | `./bin/install_terraform_cli.sh` | We would get an error of not having the correct permissions

To see the permissions of files then we can do the following: | `ls -la` | This would list the files within the directory and our file was just displayed as the following: [File and Directory Permissions](https://en.wikipedia.org/wiki/Chmod)

#### Input
```bash
ls -la ./bin/
```

#### Output
```bash
total 4
drwxr-xr-x 2 gitpod gitpod  38 Nov 19 11:49 .
drwxr-xr-x 4 gitpod gitpod 113 Nov 19 11:40 ..
-rw-r--r-- 1 gitpod gitpod 577 Nov 19 11:51 install_terraform_CLI.sh
```

#### If we try to Execute the file:

#### Input

```bash
./bin/install_terraform_CLI.sh
```

#### Output
```
bash: ./bin/install_terraform_CLI.sh: Permission denied
```

### Adding Permission to File so we can execute it

#### Input
```bash
sudo chmod a+x ./bin/install_terraform_CLI.sh 
```

### Executing file:


#### Input
```bash
./bin/install_terraform_CLI.sh
```

Now we will be able to execute the file without permission issues as we have given executable permission

#### Output
```Bash
Get:2 https://download.docker.com/linux/ubuntu jammy InRelease [48.8 kB]                                                                                                                                                                                          
Hit:3 https://ppa.launchpadcontent.net/git-core/ppa/ubuntu jammy InRelease                                                                                                                                                                                
Get:4 https://ppa.launchpadcontent.net/ondrej/apache2/ubuntu jammy InRelease [23.8 kB]                                                                                                                          
Get:5 https://ppa.launchpadcontent.net/ondrej/nginx-mainline/ubuntu jammy InRelease [23.8 kB]    
More ...                                                     
```


### Github Lifecycle (Before, Init, Command)

We need to be careful when using the Init because it will not re-run if we restart an existing workspace: [Github Lifecycle](https://www.gitpod.io/docs/configure/workspaces/workspace-lifecycle)
