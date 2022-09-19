---
title: How to upload your data to ThanoSQL workspace
---

# __How to upload your data to ThanoSQL workspace__ 

## __Preface__

ThanoSQL workspace's users can upload data using two ways as shown below.

!!! note "To upload data"
    1. Uploading your local data into your workspace environment
    2. Uploading local data by SFTP server connection using a private key

## __1. Uploading your local data in a workspace environment__

To upload local data to ThanoSQL workspace, follow these steps:

!!! note ""
    1. Log into the ThanoSQL console
    2. Click the Upload button. (Same as the Jupyter Lab method)
    3. Select the file to upload.

!!! warning "Uploading Precaution" 
    Uploading a large number of files with multiple selections can cause problems since multiple connection requests occur at once. When processing large amounts of data, be sure to upload it as a zip file.

## __2. Uploading local data by SFTP server connection using a private key__

!!! note "What is SFTP(Secure File Transfer Protocol)?"
    An interactive file transfer program with a user interface similar to File Transfer Protocol (FTP). However, SFTP uses SSH File Transfer Protocol (FTP) to create a secure connection to the server.

!!! danger ""
    - SFTP supports only ubuntu linux 
    - Though, most commands available for the linux cli are usable with SFTP, some commands are unavailable.

Follow these steps to connect to the remote server and upload data.


### __STEP 1. Verify your private key and ID__

SFTP connection requires a private key and ID. If you enter 'My Profile' at the top of the webpage, you can check or reissue your private key. Enter the workspace name for the ID required for SFTP connection.

The private key follows the format below:

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
### __STEP 2. Register private key locally - creating a pem file__

Create a file called 'key.pem' using the vim command in your local environment. Paste the private key issued in the previous step to the generated file.

```bash
$ vim key.pem
```

### __STEP 3. Change the permission of the pem file__

Change the permission of the private key registered pem file in your local environment to read.

```bash
$ chmod 400 key.pem
```

### __STEP 4. Connect to SFTP server__
After changing the permission of the file from your local environment, Connect to SFTP server.

!!! warning ""
    To connect to the SFTP server, you need the absolute path of the pem file and workspace name you checked in STEP 1.

```bash
sftp -i [the absolute path of the pem file] [name of workspace]@engine.thanosql.ai
```

Once connected, you will get a screen as shown below.

[![IMAGE](/img/thanosql_syntax/connecting/img1.png)](/img/thanosql_syntax/connecting/img1.png)

### __STEP 5. Access data files__

Once the SFTP connection is complete, locate the current location and access the authorized 'drive' folder. (You cannot access any other folder because you only have authority over the 'drive' folder.)

```bash
sftp> pwd
sftp> cd /drive
```

### __STEP 6. Transfer folders or files__

Check the remote working directory and the local working directory, and transfer the file.

```bash
put image_folder
```

By following the above steps, you can transfer image files in the local working directory 'image_folder' to the remote working directory '/drive'.

### __(Optional) SFTP Command table__
|Command|description|
|:---|:---|
|sftp remote-system| Control sftp connection of remote system.|
|sftp remote-system: file|Copy the named file from remote-system.|
|bye|End the sftp session.|
|help|List all sftp commands|
|ls|List the contents of the remote working directory.|
|lls|List the contents of the local working directory.|
|pwd|Display the name of the remote working directory.|
|cd|Change the remote working directory.|
|lcd|Change the local working directory.|
|mkdir|Create a directory on the remote computer.|
|rmdir|Delete the directory from the remote computer.|
|get|Copy files from the remote working directory to the local working directory.|
|put|Copy files from the local working directory to the remote working directory.|
|delete|Delete files from remote working directories.|
