# Group Policy Object

In Active Directory, a **Group Policy Object (GPO)** is a set of rules that control the working environment of user accounts and computers. 
GPOs allow administrators to manage settings such as desktop appearance, security restrictions, software deployment, and scripts centrally across the domain.  

This page documents the **GPOs created for the Sales Organizational Unit (OU)** in our AD lab, demonstrating **desktop customization** and **security hardening** for the `wbyer` account.

---

## GPO 1: Wallpaper Policy

### Objective
Set a custom desktop wallpaper for the `wbyer` account in the Sales OU.

### Steps Taken

1. On the server, create a folder called **wallpaper** on the desktop.  
2. Right-click the folder → **Properties → Sharing → Advanced Sharing** → Enable **Share this folder** → Share name: `wallpaper` → OK.

![sharing](https://github.com/Niharika80/Active-Directory-Project-/blob/0d38fc60e67a0f55442edef0beab2668b2e577f6/images/Screenshot%202025-12-29%20100937.png)


3. Go to **Security → Edit → Add → Authenticated Users** → OK.

![security](https://github.com/Niharika80/Active-Directory-Project-/blob/0d38fc60e67a0f55442edef0beab2668b2e577f6/images/Screenshot%202025-12-29%20101202.png)

4. Drag the image file `snowman.jpg` into the folder.
5. Open **Group Policy Management** in Active Directory.

![group](https://github.com/Niharika80/Active-Directory-Project-/blob/2880a6bba2065647a37428e82100630e893552cd/images/Screenshot%202025-12-28%20122027.png)

6. Navigate to **Sales OU → Create a new GPO**.
7. Name the GPO `wallpaper`.

![wallpaper](https://github.com/Niharika80/Active-Directory-Project-/blob/2880a6bba2065647a37428e82100630e893552cd/images/Screenshot%202025-12-29%20101722.png)

8. Right-click the `wallpaper` GPO → **Edit** →
   `User Configuration →  Policies → Administrative Templates → Desktop → Desktop → Desktop Wallpaper`

![gpo edit](https://github.com/Niharika80/Active-Directory-Project-/blob/2880a6bba2065647a37428e82100630e893552cd/images/Screenshot%202025-12-29%20102122.png)

9. Click **Enable** and set the path to the shared wallpaper:  
   ```text
   \\ADDS-0\wallpaper\snowman.jpg
   ```
10. Click OK.

![enable](https://github.com/Niharika80/Active-Directory-Project-/blob/2880a6bba2065647a37428e82100630e893552cd/images/Screenshot%202025-12-29%20104820.png)

11. Log in as the wbyer account, sign out, and log in again.
12. Verify that the wallpaper is now set to snowman.jpg.

`Before`

![Before](https://github.com/Niharika80/Active-Directory-Project-/blob/0d38fc60e67a0f55442edef0beab2668b2e577f6/images/Screenshot%202025-12-28%20201836.png)

`After` 

![Before](https://github.com/Niharika80/Active-Directory-Project-/blob/0d38fc60e67a0f55442edef0beab2668b2e577f6/images/Screenshot%202025-12-29%20105059.png)

---

## GPO 2: Block Command Prompt

### Objective
Restrict access to Command Prompt for `wbyer` in Sales OU.

### Steps Taken

1. Open **Group Policy Management**.  
2. Navigate to **Sales OU → Create a new GPO**.  
3. Name the GPO `Block_CMD`.  

![gpo name](https://github.com/Niharika80/Active-Directory-Project-/blob/2880a6bba2065647a37428e82100630e893552cd/images/Screenshot%202025-12-29%20121853.png)

4. Right-click the `Block_CMD` GPO → **Edit** →  
   `User Configuration → Policies → Administrative Templates → System → Prevent access to the command prompt`.  

![gpo edit](https://github.com/Niharika80/Active-Directory-Project-/blob/2880a6bba2065647a37428e82100630e893552cd/images/Screenshot%202025-12-29%20122342.png)

5. Double-click the policy, select **Enable**, and click **OK**.  

![gpo enable](https://github.com/Niharika80/Active-Directory-Project-/blob/2880a6bba2065647a37428e82100630e893552cd/images/Screenshot%202025-12-29%20122458.png)

6. Log in as `wbyer` on the client machine.  
7. Run `gpupdate` to apply the policy.  

![gpo update](https://github.com/Niharika80/Active-Directory-Project-/blob/2880a6bba2065647a37428e82100630e893552cd/images/Screenshot%202025-12-29%20122609.png)

8. Attempt to run `cmd.exe` to verify that Command Prompt access is blocked.

![verify cmd](https://github.com/Niharika80/Active-Directory-Project-/blob/2880a6bba2065647a37428e82100630e893552cd/images/Screenshot%202025-12-29%20122654.png)

---

## Summary

Through this lab, I learned how to create and manage **Group Policy Objects (GPOs)** in Active Directory to control user environments and enforce security settings. 
Overall, this exercise strengthened my understanding of **user management, desktop customization, and endpoint security** in a Windows Active Directory environment.
