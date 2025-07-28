# Setting up RADIUS

FIRST:
\==== Need to add server IP to allowed IP in ICE.
\===> Get with Rich and get IP added.

SERVERS:
FRDC 10.204.147.40
XRDC 10.237.76.40

**As root:**
/etc/pam\_radius.conf -- add radius servers and secret
\====== comment out 127.0.0.1
\# server\[:port\] shared\_secret timeout (s) source\_ip vrf
#127.0.0.1 secret 3
10.237.76.40:1812 vArzhj21gvG2QU4O 5
10.204.147.40:1812 vArzhj21gvG2QU4O 5
#10.27.21.99:1812 vArzhj21gvG2QU4O 5
#10.90.42.49:1812 vArzhj21gvG2QU4O 5

**\*\*NEW\*\*** **Servers** \==================
**10.237.76.40:1812** vArzhj21gvG2QU4O 5
**10.204.147.40:1812** vArzhj21gvG2QU4O 5

Old Servers:===============
10.27.21.99:1812 vArzhj21gvG2QU4O 5
10.90.42.49:1812 vArzhj21gvG2QU4O 5
\=======================

edit the /etc/pam.d/sshd
The default configuration file should look like this:
![](https://t9013498781.p.clickup-attachments.com/t9013498781/12082344-385e-4f25-8497-0957c99226dd/image.png)

Add following line appropriate for your architecture as first line of the file:
\---auth sufficient pam\_radius\_auth.so

Should look like:
\[root@XRDC-TUFIN-WORKER-1 ~\]# cat /etc/pam.d/sshd
#%PAM-1.0
auth sufficient pam\_radius\_auth.so
auth substack password-auth
auth include postlogin
account required pam\_sepermit.so
account required pam\_nologin.so
account include password-auth
password include password-auth
\# pam\_selinux.so close should be the first session rule
session required pam\_selinux.so close
session required pam\_loginuid.so
\# pam\_selinux.so open should only be followed by sessions to be executed in the user context
session required pam\_selinux.so open env\_params
session required pam\_namespace.so
session optional pam\_keyinit.so force revoke
session optional pam\_motd.so
session include password-auth
session include postlogin

**Add users into /etc/group file:**
\=== add users
adduser -G wheel,ssh\_users tfn9845
adduser -G wheel,ssh\_users kqo7995
adduser -G wheel,ssh\_users mhu8789
adduser -G wheel,ssh\_users mge3848
adduser -G wheel,ssh\_users khu6774
adduser -G wheel,ssh\_users pwe6654
adduser -G wheel,ssh\_users bil9244

Update SUDOERS file with:
./sudoers:#%wheel ALL=(ALL) ALL
./sudoers:%wheel ALL=(ALL) NOPASSWD: ALL

1\. Edit the /etc/ssh/sshd\_config file to enable PAM authentication in ssh service.
1\. Make sure ChallengeResponseAuthentication yes is enabled and does not include a #.
2\. Ensure that UsePAM yes is enabled and does not include a #.
3\. Save and quit.
2\. Restart the SSH server service to apply new configurations:
3\. systemctl restart sshd

SHOW PAM/SSHD logs:
journalctl -u sshd -n 50
journalctl -u sshd | tail

[https://forum.tufin.com/support/kc/latest/Content/Suite/8256.htm](https://forum.tufin.com/support/kc/latest/Content/Suite/8256.htm)

[https://docs.secureauth.com/2104/en/pam-radius-installation-and-configuration-guide.html](https://docs.secureauth.com/2104/en/pam-radius-installation-and-configuration-guide.html)