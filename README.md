# Configuring a VPN Client for Secure File Transfers

## Objective
The objective of this lab was to configure a VPN client on a Windows Server 2019 machine to connect to an L2TP/IPsec server on a pfSense firewall-router. Additionally, the lab aimed to compare secure and non-secure file transfers using Wireshark to analyze the differences in packet data.

### Skills Learned
- Configuration of VPN clients using Windows and PowerShell.
- Understanding of L2TP/IPsec VPN setup and encryption protocols.
- Proficiency in using Wireshark for packet capture and analysis.
- Ability to compare secure (SSH) and non-secure (FTP) file transfers.
- Practical experience with VPN setup and secure file transfer protocols.

### Tools Used
- pfSense
- PowerShell
- PuTTY
- Wireshark

## Steps

## Section 1: Hands-On Demonstration

### Part 1: Configure a Windows VPN Client

1. Open Network and Sharing Center:
   - On the RemoteWindows01 desktop, right-click the Network icon and select Properties to open the Network and Sharing Center window.

2. Set Up a New Connection:
   - In the Network and Sharing Center window, click Set up a new connection or network.
   - On the Choose a connection option page, select Connect to a workplace and click Next.
   - On the How do you want to connect? page, click Use my internet connection (VPN).

3. Specify the VPN Server:
   - On the Type the Internet address to connect to page, type `202.20.1.1` in the Internet address field.
   - In the Destination name field, delete the existing text and type `yourname IPsec`.

4. Create the Connection:
   - Click Create.

5. Change Adapter Settings:
   - In the Network and Sharing Center, click Change adapter settings in the left pane.

6. Open Connection Properties:
   - In the Network Connections window, right-click the `yourname IPsec` connection and select Properties.

7. Configure Security Settings:
   - In the Properties dialog box, click the Security tab.
   - From the Type of VPN drop-down menu, select IKEv2.
   - From the Data encryption drop-down menu, select Require Encryption (disconnect if server declines).
   - In the Authentication section, click the Use Extensible Authentication Protocol (EAP) radio button.

8. Disable IPv6 and Set Up IPv4:
   - In the Properties dialog box, click the Networking tab.
   - De-select the Internet Protocol version 6 (TCP/IPv6) checkbox.
   - Highlight Internet Protocol Version 4 (TCP/IPv4) and click Properties.
   - Click the Advanced button.
   - De-select Use default gateway on remote network.

9. Finalize Configuration:
   - Click OK to save the changes in each dialog box and return to the Network Connections window.

10. Open Network and Internet Settings:
    - From the lower-right corner of the RemoteWindows01 taskbar, click the Network Settings icon and select Network and Internet settings.

11. View Available VPN Connections:
    - From the left panel, click VPN.

12. Edit VPN Connection Properties:
    - Click the `yourname IPsec` connection and then click Advanced options.
    - Click Edit and enter the following credentials:
      - User name (optional): `remoteworker01`
      - Password (optional): `password1`
    - Click Save.

Ref 1 - the VPN connection properties.

![image](https://github.com/user-attachments/assets/886d8eae-e907-433a-8a2a-2f982dd4a259)

13. Install the VPN Server's Certificate:
    - From the RemoteWindows01 taskbar, click the File Explorer icon.
    - Navigate to `C:\certs`.
    - Double-click the `IPsecCA.crt` file.
    - In the Certificate dialog box, click Install Certificate.
    - In the Certificate Import Wizard, select Local Machine and follow the prompts to place the certificate in the Trusted Root Certification Authorities store.

14. Add a VPN Connection Route:
    - On the RemoteWindows01 taskbar, click the PowerShell icon.
    - At the PowerShell prompt, type:
      ```powershell
      Add-VpnConnectionRoute -ConnectionName "yourname IPsec" -DestinationPrefix 172.30.0.0/24 -PassThru
      ```
    - Replace `yourname` with your own name.

15. Connect to the VPN Server:
    - From the RemoteWindows01 taskbar, restore the Settings window.
    - Click the `yourname IPsec` connection and click Connect.

16. Test VPN Connectivity:
    - On the RemoteWindows01 desktop, right-click the Network icon and select Properties.
    - Restore the PowerShell window.
    - At the PowerShell prompt, type:
      ```powershell
      ping 172.30.0.2
      ```

Ref 2 - the successful ping response.

![image](https://github.com/user-attachments/assets/23f96d9e-c462-46b5-9109-76d51bf91d8a)

17. Run Tracert to Verify Split Tunnel Configuration:
    - At the PowerShell prompt, type:
      ```powershell
      tracert 202.20.1.2
      ```

18. Disconnect the VPN and Reset the Default Gateway:
    - From the RemoteWindows01 taskbar, restore the Settings window.
    - Under the `yourname IPsec` connection, click Disconnect.
    - In the Network and Sharing Center, click Change adapter settings.
    - Right-click the `yourname IPsec` adapter and select Properties.
    - In the Networking tab, highlight Internet Protocol Version 4 (TCP/IPv4) and click Properties.
    - Click the Advanced button.
    - Re-enable Use default gateway on remote network.
    - Save changes and reconnect to the VPN.

19. Perform Another Tracert:
    - At the PowerShell prompt, type:
      ```powershell
      tracert 202.20.1.2
      ```

Ref 3 - your new tracert results.

![image](https://github.com/user-attachments/assets/980c4fef-119e-4ce1-8a39-86f96fb6c284)

### Part 2: Compare Secure and Non-Secure File Transfers in Wireshark

1. Open a Connection to the vWorkstation System:
   - On the Lab View toolbar, select `vWorkstation` from the Virtual Machine menu.

2. Open File Explorer on vWorkstation:
   - Navigate to `C:\PCAPs` and double-click the `ftp-capture` file to open it in Wireshark.

3. Create an FTP Filter:
   - In the Wireshark Filter box, type `ftp` and press Enter.

4. Review Specific Packets:
   - Click packets 12, 13, 15, 16, 17, 30, 49, and 61-69 in sequence, exploring details for each.

Ref 4 - the packet that carries the correct password.

![image](https://github.com/user-attachments/assets/3ea04f4f-a0ed-44cd-9966-7e3f369152eb)

5. Create a New Filter for ftp-data:
   - In the Filter box, type `ftp-data` and press Enter.

Ref 5 - the Wireshark window and the packet bytes pane for Packet 69.

![image](https://github.com/user-attachments/assets/9a5fb74d-4c25-49d7-959e-4b65796127c9)

6. Open and Filter the ssh-capture File:
   - From the Wireshark menu bar, click File, select Open, and double-click the `ssh-capture` file.
   - Create a new filter by typing `ssh` in the Filter box and pressing Enter.

7. Review Specific SSH Packets:
   - Click packets 12, 13, 15, 18, 21, 22, 26, and 79 in sequence, exploring details for each.

Ref 6 - the filtered SSH packets in your capture file.

![image](https://github.com/user-attachments/assets/e2a55254-3e13-4277-85e0-461614ac1caf)

8. Open and Filter the ipsec-capture File:
    - From the Wireshark menu, click File, select Open, and double-click the `ipsec-capture` file.
    - Clear the filter and click packet 1, exploring details for each relevant packet.

9. SCREENSHOT 7 - the packet details pane for packet 16.

![image](https://github.com/user-attachments/assets/a7bfb02a-86dd-4302-b8e2-c1a49d7fb981)

Ref 8 - the last SSHv2 packet in the SSH file transfer.

![image](https://github.com/user-attachments/assets/e2a55254-3e13-4277-85e0-461614ac1caf)

10. Create a New Filter for esp Packets:
    - In the Filter box, type `esp` and press Enter.

Ref 9 - the last packets in the ESP exchange.

![image](https://github.com/user-attachments/assets/324d70e3-07be-4193-a44d-0a78d9114100)

## Section 2: Applied Learning

### Part 1: Configure a Windows VPN Client

1. Reset the Virtual Environment if you have completed Section 1:
   - Click Options > Reset Lab or Disconnect > Discard Changes.

2. Launch Windows PowerShell on RemoteWindows01.

3. Add a VPN Connection Using PowerShell:
   - Execute the following command:
     ```powershell
     Add-VpnConnection -Name "yourname_IPsec_S2" -ServerAddress "202.20.1.1" -TunnelType IKEv2 -EncryptionLevel Required -AuthenticationMethod EAP -SplitTunneling -AllUserConnection
     ```

4. Add a Route Using PowerShell:
   - Execute the following command:
     ```powershell
     Add-VpnConnectionRoute -ConnectionName "yourname_IPsec_S2" -DestinationPrefix 172.30.0.0/24 -PassThru
     ```

5. Install the VPN Server’s Certificate:
   - Follow steps similar to those in Section 1 to install the certificate from `C:\certs`.

6. Connect to the VPN and Verify Encryption:
   - Connect to the `yourname_IPsec_S2` VPN.
   - Open the Network and Sharing Center, select the `yourname_IPsec_S2` connection, and check the Details tab for AES 256 encryption.

Ref 10 - the IPsec VPN connection encrypted with AES 256.

![image](https://github.com/user-attachments/assets/445ea037-1096-4fd4-bbde-0353fc807096)

7. Verify Connectivity Using Tracert:
   - At the PowerShell prompt, execute:
     ```powershell
     tracert 172.30.0.2
     ```

Ref 11 - your successful tracert to the remote machine.

![image](https://github.com/user-attachments/assets/7006eb4d-0bb7-4df4-b5d0-fbab607bee2f)

---

### Part 2: Compare Secure and Non-Secure File Transfers in Wireshark

1. Generate Traffic to Analyze:
   - On RemoteWindows01, launch Wireshark and start a packet capture.
   - Connect to the `yourname_IPsec` VPN and then stop the capture.
   - Filter for `isakmp` protocol packets.

Ref 12 - the IKE_SA_INIT and IKE_AUTH packets.

![image](https://github.com/user-attachments/assets/bc8e27fb-25e3-40be-88cc-9eead6ef3ba3)

2. Transfer Files Using FTP:
   - On vWorkstation, start a Wireshark packet capture.
   - Open a Command Prompt and use `ftp` to connect to `172.40.0.20` and transfer `file.txt`.

3. Transfer Files Using SSH:
  

 - On vWorkstation, use PuTTY to connect to `172.40.0.20` and transfer files using SSH.

4. Filter for `ftp` and `ftp-data` Packets:
Ref 13 - the filtered FTP packets in your capture file.

![image](https://github.com/user-attachments/assets/34154005-b57f-4753-8bd1-1525f7de53d1)

Ref 14 - the contents of the `file.txt` file in the packet bytes pane.

![image](https://github.com/user-attachments/assets/6ae5cb1b-8d9c-4d27-aa9b-e2c919a24bdd)

5. Filter for `ssh` Protocol Packets:

Ref 15 - the filtered SSH packets in your capture file.

![image](https://github.com/user-attachments/assets/b7f64ccc-520a-4f19-b34d-cb85cd72be80)

---

## Section 3: Challenge and Analysis

### Part 1: Create a New VPN Connection Using PowerShell

1. Create a Split-Tunnel VPN Connection:
   - On vWorkstation, use PowerShell to create a VPN connection named `yourname_IPsec2`.

### Part 2: Implement a Custom IPsec Policy

1. Apply IPsec Configuration:
   - Use PowerShell to set the IPsec configuration for `yourname_IPsec2`.

### Part 3: Verify Your VPN Implementation Using Wireshark

1. Analyze isakmp Packets:
   - Open Wireshark and start a packet capture.
   - Connect to the VPN and filter for `isakmp` frames.

Ref 16 - the CREATE_CHILD_SA exchange.

![image](https://github.com/user-attachments/assets/7bd5e2bd-94c3-41eb-85aa-3a8dba50315d)

Ref 17 - the selected Diffie-Hellman transform.

![image](https://github.com/user-attachments/assets/55c9ad82-7eb0-4232-87b6-d69e69ce65e4)

## Key Takeaways
This lab provided practical experience in configuring VPN clients and understanding secure vs. non-secure file transfers. The hands-on tasks with Windows VPN client setup, PowerShell configuration, and Wireshark analysis emphasized the importance of VPNs for secure communications. Comparing FTP and SSH traffic highlighted the vulnerabilities of non-secure protocols and the advantages of using encrypted channels for data transfer. This lab reinforced the critical skills needed for configuring and securing VPN connections in a professional environment.
```
