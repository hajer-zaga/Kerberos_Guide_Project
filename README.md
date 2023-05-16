# Client Authentication Using Kerberos for Access to an SSH Service 

###### This guide project takes approximately 10 minutes to read.

## Introduction 

In order to ensure secure access to a SSH service, this project has been designed to guide the authentication of a client using Kerberos. Kerberos provides a centralized authentication system, where clients can obtain tickets from a Kerberos server to access various services. By utilizing Kerberos, the project aims to improve the security of the authentication process for clients accessing the SSH service. The project involves setting up a Kerberos server and configuring it to work with the SSH service. Clients will then need to obtain a ticket from the Kerberos server before being able to access the SSH service. With this project, the authentication process will be more efficient and secure, giving clients peace of mind when accessing sensitive information through the SSH service. 

## Tools

- Oracle VM VirtualBox 6.1 

- Ubuntu 22.04 (for the key distribution center) @ip 👉 ‘192.168.56.107’ 

- Ubuntu 22.04 (for the client) @ip 👉 ‘192.168.56.106’


## Kerberos Protocol

### Introduction

Kerberos protocol. It's a popular protocol. It's built into Windows and a lot of different software. A group of students at MIT. Who decided to pull passwords off the network. Meaning, if Alice wants to log into Bob over the MIT network, or any campus or enterprise network, so they build a protocol to make that work. And what they required was something called a key distribution center "KDC".

- First, there's a key distribution center, and then there's clients and servers.

- The key distribution center sets up private keys with each participant.

- Alice gets some (K sub a) that the key distribution center gives to Alice.

- Bob the server gets (K sub b) that the key distribution center gives to Bob, and so on for everyone.

![Reference Image](/screenshots/capture%20partie%201/Introduction.png)


- Also, there are clocks. Everybody's got a synchronized clock. That's going to be important, because it'll help us in some sense make sure that we don't have sort of cheating in the protocol. So everybody follow, we've got clocks, everybody's got keys.

![Reference Image](/screenshots/capture%20partie%201/synchronizedClock.png)


⚠ And the idea is that, Alice, Bob, whomever's using Kerberos, it's okay to type a password into a local machine. But we don't want passwords on the network. That's the goal. Keep passwords off the local area network because as you've learned, anybody with a sniffer, with a computer that just accepts packets or messages, can read your password. So it's not a good idea passwords flying over the network.

### So here's the way Kerberos works 👇


- Alice typed the password in to Kerberos, logs in, Kerberos checks it. No passwords over networks, but you're kind of quote unquote logging into Kerberos.


- Alice's machine will send the standard message to the key distribution center, saying Alice needs the ticket generating ticket TGT. It's like a ticket to get tickets to get to Bob.


- TGT will generate a session key for Alice. Alice's (K sub a) is called the root key, that's a base secret we don't want anybody to know. But once Alice wants to try logging into something, we're going to use the base secret, (K sub a), to generate a more temporary secret called a session secret or session key. So think of it essentially as a message that's encrypted, signed by the key distribution center.


- But Rather than signing it the way you might expect using the KDC's key, it's going to Sign it with Alice's key, And then send it over to Alice.

![Reference Image](/screenshots/capture%20partie%201/kerberos_work_steps5.png)

- Now if Eve is watching what's going on, she's not going to be able to read the message that goes back to TGT because it's signed with Alice's key (K sub a). And in there will be this temporary session key.

![Reference Image](/screenshots/capture%20partie%201/eve_intercept_5.png)

- Now, Alice would like to request some way of communicating with Bob. Logging into Bob, and doesn't want to use a password 🤔 

 ⚠ The ticket that you'll get for Bob as a ticket to Bob, we'll call it TBOB 🤓 

- In this step Alice, basically, says to the Key Distribution Center, "Hey, I'd like to connect to TBOB. Can you give me something there?" And I'm also going to use the clock to sort of synchronize using the session key they gave me last time.

- we're going to use our session key now for communication.

- the clock encrypted, with my request to connect to Bob

- ⚠ I don't have to encrypt that request because Kerberos is not about traffic analysis prevention, it's about keeping passwords off the network.

- KDC "Key Distribution Center" creates a joint session key for me and Bob.![Reference Image](/screenshots/capture%20partie%202/kerberos_9_steps.png)

- Bad actors like Eve are interested in monitoring the communication with Bob. However, Kerberos protocol ensures that Eve can't see our communication.![Reference Image](/screenshots/capture%20partie%202/kerberos_9_steps_eve.png)

- Finally Kerberos has tended to be much more useful for enterprise campus applications than for big, old, internet-type things and why? The reason is public key cryptography, which turns out to be so much more flexible and scalable.




## ⚠ The project implementation.

## Part 1️⃣

1. Calling the 1st machine that have hostname "kdc.centrale.tn", with their ip address "192.168.56.107".

![Reference Image](/screenshots/implimentation/hostname_machine_kdc.png)

![Reference Image](/screenshots/implimentation/ip_machine_kdc.png)

2. Calling the 2nd machine that have hostname "client.centrale.tn", with their ip address "192.168.56.106".

![Reference Image](/screenshots/implimentation/hostname_machine_client.png)

![Reference Image](/screenshots/implimentation/ip_machine_client.png)

3. Configure communication between the two machines.

Ping kdc-client 👇
![Reference Image](/screenshots/implimentation/ping_kdc_client.png)

Ping client-kdc 👇
![Reference Image](/screenshots/implimentation/ping_client_kdc.png)

- ⚠ To successfully establish a ping connection between the two machines, it is necessary to verify the network configuration of the virtual machine (VM) as well as the network configurations of the two Ubuntu machines being used.

For the VM 👇

![Reference Image](/screenshots/partie1/NAT_VM_conf.png)

For Ubuntu kdc+client machines👇

![Reference Image](/screenshots/partie1/config_reseau_machine_NAT.png)
![Reference Image](/screenshots/partie1/config_reseau_machine_Reseau_Prive_Hote.png)


4. Installing Kerberos in the kdc machine

⚠ Kerberos has two main constraints: 

- Build a DNS ✔ 

![Reference Image](/screenshots/implimentation/hostname_machine_kdc.png)


![Reference Image](/screenshots/implimentation/ip_machine_kdc.png)


![Reference Image](/screenshots/implimentation/hostname_machine_client.png)


![Reference Image](/screenshots/implimentation/ip_machine_client.png)


- Clock synchronization ✔ 

Clock synchronization for kdc machine
![Reference Image](/screenshots/partie1/Clock_sys_kdc.png)

Clock synchronization for client machine
![Reference Image](/screenshots/partie1/Clock_sys_client.png)

Kerberos installation on sudo mode :
```
apt install krb5-kdc krb5-admin-server krb5-config
```

Kerberos configuration :

![Reference Image](/screenshots/partie1/conf_kerberos1.png)

![Reference Image](/screenshots/partie1/conf_kerberos_servers.png)

![Reference Image](/screenshots/partie1/conf_kerberos_admi.png)

![Reference Image](/screenshots/partie1/conf_kerberos_settingUp.png)


To Initialize the database for "CENTRALE.TN" :
```
krb5_newrealm
```

Enter a password : 

![Reference Image](/screenshots/partie1/realm_database.png)

Access to acl file :
```
nano kadm5.acl
```

Uncomment this line :
```
*/admin*
```

Modify it :
```
*/admin@centrale.tn*
```

![Reference Image](/screenshots/partie1/kadm5.acl.png)

- We have successfully completed the configuration of the Kerberos server. ✅

5. Creation of principals

Here is a list of principals that we can start working with:

- Add user :

```
add_principal utilisateur
```

- Add root/admin :

```
add_principal root/admin
```

- Add host :

```
add_principal host/kdc.centrale.tn
```

```
list_principals
```
![Reference Image](/screenshots/partie1/host_principal_created.png)



6. Create a keytab file :

- Check the keytab path :

```
cd /etc/krb5kdc/
```
```
ls
```
```
nano kdc.conf
```

![Reference Image](/screenshots/partie1/kitab_path.png)

- Currently, the keytab file does not exist :

![Reference Image](/screenshots/partie1/no_keytab_file.png)

To create the keytab file, you can run this command-line 👇
```
add_entry –password –p root/admin@CENTRALE.TN -k 1 –e aes256-cts-hmac-shal-96
```

The keytab file has been successfully added ✅

![Reference Image](/screenshots/partie1/keytab_file_add.png)

![Reference Image](/screenshots/partie1/affich_file_keytab.png)

- Add keytab to host principal : 

```
addent –password –p host/kdc.centrale.tn -k 1 –e aes256-cts-hmac-shal-96
```

![Reference Image](/screenshots/partie1/root_host_added.png)


7. Restart the server :

```
systemctl restart krb5-kdc
```
```
systemctl restart krb5-admin-server
```


## Part 2️⃣

- In this part, we will install OpenSSH in KDC machine and configure its configuration file, and then restart the server. 

1. install OpenSSH : (with sudo mode)

```
apt install openssh-server
```

2. Edit file Configuration :

```
nano /etc/ssh/sshd_config
```

![Reference Image](/screenshots/partie2/uncom_GSSAPI.png)

```
nano /etc/ssh/ssh_config
```

![Reference Image](/screenshots/partie2/uncom_GSSAPI_ssh.png)

3. Restart OpenSSH server :

```
systemctl restart sshd
```

## Part 3️⃣

- In this third part, we used two machines to authenticate a client to an SSH service using a TGT ticket provided by a KDC.

1. Install Kerberos on the client machine :

```
apt install krb5-user
```

Provide the server : 
```
kdc.centrale.tn
```
Provide the administrator : 
```
kdc.centrale.tn
```

2. Install the OpenSSH server :

```
apt install openssh-server
```

Modify the configuration file :
```
nano /etc/ssh/sshd_config
```

(first modification) Uncomment the credentials, you need to remove the '#' symbol from the credential lines in sshd configuration file :
```
# GSSAPIAuthentication yes 

# GSSAPICleanupCredentials yes 
```

(second modification) Uncomment the credentials, you need to remove the '#' symbol from the credential lines in SSH configuration file :
```
# GSSAPIAuthentication yes 

# GSSAPIDelegateCredentials yes 
```

Restart the system :
```
systemctl restart sshd
```

- The SSH configuration has been successfully completed. ✔

3. Add user for the kdc machine :

Display list of principals :
```
kadmin.local
```
```
list_principals
```
![Reference Image](/screenshots/partie3/list_principals_kdc.png)

Add user :
```
adduser_utilisateur
```
![Reference Image](/screenshots/partie3/add_user_client.png)

Access to the user :
```
su -l utilisateur
```
![Reference Image](/screenshots/partie3/ent_to_user.png)

User's list of kdc machine :

![Reference Image](/screenshots/partie3/user_machine_kdc.png)

4. Get the ticket :

At the moment, we don't have a ticket :
```
klist
```
![Reference Image](/screenshots/partie3/no_tiket_client.png)

After running this command :
```
ssh kdc.centrale.tn
```
![Reference Image](/screenshots/partie3/ent_to_kdc_from_client.png)

It will ask if you want to access the machine and prompt you for a password. However, we will not set a password to access the machine kdc.centrale.tn, We will generate a ticket.


5. Access the machine with TGT ticket provided by a KDC :

Generate the ticket :
```
kinit
```
Once it's done, enter the password for the user (utilisateur@CENTRALE.TN) :

![Reference Image](/screenshots/partie3/ent_pw_of_user_in_client_machine.png)

And here is the TGT ticket :
```
klist
```
![Reference Image](/screenshots/partie3/tiket_tgt.png)

Now, we can access to the kdc machine using SSH :
```
ssh kdc.centrale.tn
```
![Reference Image](/screenshots/partie3/ent_to_kdc_with_tiket.png)

And now, we can access the machine without providing the password of the KDC machine because we are authorized to do that. We have a ticket that authenticates us. ✅


##### Useful video tutorial  :
###### Video 1: <https://www.youtube.com/watch?v=vx2vIA2Ym14&list=PLl3CtU4THqPbCtbTUuuWmVlo__3rlHngN&index=33>
###### Video 2: <https://www.youtube.com/watch?v=cMIYi7mnHUY&list=PLl3CtU4THqPbCtbTUuuWmVlo__3rlHngN&index=35>



    




