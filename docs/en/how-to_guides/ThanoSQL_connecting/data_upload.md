---
title: How to import data
---

# **How to import data**

## Preface

- Updated Date : {{ git_revision_date_localized }}

## **Overview**

Users can import data in two ways:

!!! note "How to load a dataset"
    1. Upload a local dataset from a workspace environment
    2. Use a private key to connect SFTP to load a local dataset

## **1. Loading data into the workspace environment**

To upload a local data set to the ThanoSQL workspace, follow these steps.
!!! note "" 
    1. Log in to the ThanoSQL console.
    2. Click the Upload button. (same as the Jupyter Lab method)
    3. Select a file to upload.

## **2. How to access and retrieve data from a remote system(SFTP)**

!!! note "SFTP(Secure File Transfer Protocol)?"
    Indicates an interactive file transfer program with a user interface similar to File Transfer Protocol (FTP). However, SFTP uses SSH File Transfer Protocol (FTP) to create a secure connection to the server.

!!! danger ""
    - SFTP supports Ubuntu and Linux environments only.
    - Some of the options available as commands are not included in SFTP commands, but most commands are included.

Follow these steps to access and retrieve data from a remote system.

### **STEP 1. How to verify your private key and ID**

SFTP connection requires a private key and ID. You can verify or reissue your private key by typing `My Profile` at the top of the web page.
Enter the workspace name for the ID required for the SFTP connection.

The private key consists of the following types.

```pem
-----BEGIN OPENSSH PRIVATE KEY-----
b3BlbnNzaCtdjEAAAAABG5vbmUtesttesttestteZQAAAAAAAAABAAABlwAAAAdzc2gtcn
NhAAAAAwEAEAwf0AHzaPEz3m81l56ScriVThc4v/9/erUZQtaRkT6TRasdasdrY96O4AZE
3bprMXloa1d73wESoGasdsaneoznug3XEufi2r3Iom7QKS1yF90sf3SSsaU8LGadMLQ5fd
VEHDk2K94Y6120IH/SUs8mKnh1DiasdoplgbLrWv90TYLl7WAInyruads0hYQTedZhdWYz
WLZlSO0bac+8N8SPHLFr2VgKUmzZatpdasJwIk6nAP23aYuYD5GIH/cEQvvquhSNsaHKhm
AdnUS6MlSQxD2LOnBEPNdadzzseJs9fPJtR0Zgdw9sFzm24c/0ixxs7RUd+56hbyviadow
iAkUmA6BXP49vK4VcF9asdGTKCdoEvDBwXJ+fEpAAAFkAFunosBbp6LAAAAB3NzaC1yca2
iAkUmA6BXP49vK4VcF9asdGTKCdoEvDBwXJ+fEpAAAFkAFunosBbp6LAAAAB3NzaC1yca2
iAkUmA6BXP49vK4VcF9asdGTKCdoEvDBwXJ+fEpAAAFkAFunosBbp6LAAAAB3NzaC1yca2
iAkUmA6BXP49vK4VcF9asdGTKCdoEvDBwXJ+fEpAAAFkAFunosBbp6LAAAAB3NzaC1yca2
EAAMH9AB82jxM95vNZeeknK4lU4XOdas/dE2C5e1gCJ8q7tIWEE3mYXVmM1i2ZUjtAEdhB
KG3Ejxyxa9lasdaYClJadasybUdGYHcPbBc5tuHP9IscbO0VHfueoW8r4qMIgBUBYip1BR
7lEc2xXpOA23asdasdgzxMzUGvpuzvBJg0oeW3J6NiDtOVdyKnUpjo6Off5QTeoRRQ1+Sd
0HKZ32q7f0yozK9gRublbeilsTzEkWhvnDz7aobfsTiN2JXvGp3rx7manR4adsasds/JrM
u9O1QqfvqUTcd++ax1WGk5CPvsCYLninZXXqR7y8IQ5HWRIFEBsI4rLfoJmmKZgepTa2zw
d0mmCMjP5HzeikNoLJhyunlOTUUtoIzwAAAMEAyUD2N93Iwf09rlNYrOURANjXJ7JLnVOT
iqgFRgzG7ELjBlVknasdasdVyQSwXqPQeV0LKqIK/pro3u84c20U9G3Hj6/RU/spVgMFLu
yQmkvxp//a7emwLtUgOa76IYckBePZUduaELPWdjVOliajZyOEIlUPslD7cTr1zLffhNBx
l2J5BknmWy4lng3m44vE2f7ZmDCdmopK6EXQs3vPxbJkaROUQTEqQAsBFLU1KkkgAJDkGc
kp1GuW0kIgRvSHAAAAE3Jvb3RAdGVzdGluZy1zZXJ2ZXIBAgMEBQYHasdasdasdasdsdww
-----END OPENSSH PRIVATE KEY-----
```

### **STEP 2. Register private key locally - create pem file**

Create the `key.pem` file using the vim command in the local environment.
Paste the private key issued in the previous step into the generated file.

```bash
$ vim key.pem
```

### **STEP 3. To change pem file permissions**

Change the permissions of pem files registered with the private key so that they can be read in the local environment.

```bash
$ chmod 400 key.pem
```

### **STEP 4. Connecting SFTP**

Connect to SFTP when file permissions are completely changed in your local environment.
!!! warning ""
    To access SFTP, you need the absolute path and workspace name of the pem file that you verified in step 1.

```bash
sftp -i [Absolute path to the pem file] [Workspace Name]@engine.thanosql.ai
```

Once connected, you can check the screen below.

[![IMAGE](/img/thanosql_syntax/connecting/img1.png)](/img/thanosql_syntax/connecting/img1.png)

### **STEP 5. Accessing data files**

Once the SFTP connection is complete, locate the current location and access the authorized 'drive' folder.
(You cannot access because you are not authorized except for the 'drive' folder.)

```bash
sftp> pwd
sftp> cd /drive
```

### **STEP 6. To transfer folders or files**

Check the remote and local working directories and transfer the file.

```bash
put image_folder
```

The steps above allow you to transfer image files from the local working directory `image_folder` to the remote working directory `/drive`.

### **(Optional) SFTP Command Table**

| Command                  | Description                                                                     |
| :----------------------- | :------------------------------------------------------------------------------ |
| sftp remote-system       | Establish an sftp connection to the remote computer.                            |
| sftp remote-system: file | Copy the named file from the remote system.                                     |
| bye                      | Terminates the sftp session.                                                    |
| help                     | Lists all sftp commands.                                                        |
| ls                       | Lists the contents of the remote working directory.                             |
| lls                      | Lists the contents of the local working directory.                              |
| pwd                      | Displays the name of the remote working directory.                              |
| cd                       | Change the remote working directory.                                            |
| lcd                      | Change the local working directory.                                             |
| mkdir                    | Create a directory on the remote system.                                        |
| rmdir                    | Delete the directory from the remote system.                                    |
| get                      | Copy the file from the remote working directory to the local working directory. |
| put                      | Copy the file from the local working directory to the remote working directory. |
| delete                   | Delete the file from the remote working directory.                              |

<br>
