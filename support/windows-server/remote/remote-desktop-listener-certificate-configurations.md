---
title: Remote Desktop listener certificate configurations
description: Describes the methods to configure RDP listener certificates in Windows Server 2012 R2 and Windows Server 2012.
ms.date: 06/27/2025
manager: dcscontentpm
audience: itpro
ms.topic: troubleshooting
ms.reviewer: kaushika
ms.custom:
- sap:remote desktop services and terminal services\licensing for remote desktop services (terminal services)
- pcy:WinComm User Experience
zone_pivot_groups: rdp-windows-server-versions
---
# Remote Desktop listener certificate configurations

This article describes the methods to configure listener certificates on a Windows Server that isn't part of a Remote Desktop Services (RDS) deployment.

_Original KB number:_ &nbsp; 3042780

## About Remote Desktop server listener availability

The listener component runs on the Remote Desktop server and is responsible for listening to and accepting new Remote Desktop Protocol (RDP) client connections. This lets users establish new remote sessions on the Remote Desktop server. There's a listener for each Remote Desktop Services connection that exists on the Remote Desktop server. Connections can be created and configured by using the Remote Desktop Services Configuration tool.

## Configure Remote Desktop server listener certificate

### [WMI](#tab/wmi)

The configuration data for the RDS listener is stored in the `Win32_TSGeneralSetting` class in Windows Management Instrumentation (WMI) under the `Root\CimV2\TerminalServices` namespace.

The certificate for the RDS listener is referenced through the **Thumbprint** value of that certificate on a **SSLCertificateSHA1Hash** property. The thumbprint value is unique to each certificate.

> [!NOTE]
> Before you run the commands, the certificate that you want to use must be imported to the **Personal** certificate store for the computer account (via `certlm.msc`). If you don't import the certificate, you'll receive an **Invalid Parameter** error.

To configure a certificate by using WMI, follow these steps:

1. Open the properties dialog for your certificate and select the **Details** tab.

::: zone pivot="windows-server-pre-2012"

2. Scroll down to the **Thumbprint** field and copy the space-delimited hexadecimal string into something like Notepad.

   The following screenshot shows an example of the certificate thumbprint in the **Certificate** properties:

   :::image type="content" source="./media/remote-desktop-listener-certificate-configurations/thumbprint-property.png" alt-text="An example of the certificate thumbprint in the Certificate properties.":::

   If you copy the string into Notepad, it should resemble the following screenshot:

   :::image type="content" source="./media/remote-desktop-listener-certificate-configurations/thumbprint-string-in-notepad.png" alt-text="Copy and paste the thumbprint string into Notepad.":::

   After you remove the spaces in the string, it still contains the invisible ASCII character that is only visible at the command prompt. The following screenshot is an example:

   :::image type="content" source="./media/remote-desktop-listener-certificate-configurations/ascii-character-in-command-prompt.png" alt-text="The invisible ASCII character that is only shown at the command prompt." border="false":::

   Make sure that this ASCII character is removed before you run the command to import the certificate.

3. Remove all spaces from the string. There may be an invisible ACSII character that is also copied. This character isn't visible in Notepad. To validate the string, copy the string directly into the Command Prompt window.

4. At command prompt, run the following `wmic` command together with the thumbprint value that you obtain in step 3:

   ```console
   wmic /namespace:\\root\cimv2\TerminalServices PATH Win32_TSGeneralSetting Set SSLCertificateSHA1Hash="THUMBPRINT"
   ```

    The following screenshot shows a successful example:

   :::image type="content" source="./media/remote-desktop-listener-certificate-configurations/successful-example-to-run-wmic-commands.png" alt-text="A successful example of running the `wmic` command together with the thumbprint value that you obtain in step 3." border="false":::

::: zone-end
::: zone pivot="windows-server-2012"

2. Scroll down to the **Thumbprint** field and copy the space-delimited hexadecimal string into a text editor like Notepad.

   The following screenshot shows an example of the certificate thumbprint in the **Certificate** properties:

   :::image type="content" source="./media/remote-desktop-listener-certificate-configurations/thumbprint-property.png" alt-text="An example of the certificate thumbprint in the Certificate properties.":::

   When you copy the string into Notepad, it should look like the following screenshot:

   :::image type="content" source="./media/remote-desktop-listener-certificate-configurations/thumbprint-string-in-notepad.png" alt-text="Copy and paste the thumbprint string into Notepad.":::

   After you remove the spaces in the string, it still contains an invisible ASCII character that is only visible at the command prompt. The following screenshot shows an example:

   :::image type="content" source="./media/remote-desktop-listener-certificate-configurations/ascii-character-in-command-prompt.png" alt-text="The invisible ASCII character that is only shown at the command prompt." border="false":::

   Ensure that this ASCII character is removed before you run the command to import the certificate.

3. Remove all spaces from the string. There might be an invisible ACSII character that is also copied. This character isn't visible in Notepad. To validate the string, copy the string directly into the Command Prompt window.

4. At command prompt, run the following `wmic` command together with the thumbprint value that you obtain in step 3:

   ```console
   wmic /namespace:\\root\cimv2\TerminalServices PATH Win32_TSGeneralSetting Set SSLCertificateSHA1Hash="THUMBPRINT"
   ```

    The following screenshot shows a successful example:

   :::image type="content" source="./media/remote-desktop-listener-certificate-configurations/successful-example-to-run-wmic-commands.png" alt-text="A successful example of running the `wmic` command together with the thumbprint value that you obtain in step 3." border="false":::

::: zone-end
::: zone pivot="windows-11-or-server-2025"

2. Scroll down to the **Thumbprint** field and copy it. The following screenshot is an example of the certificate thumbprint in the **Certificate** properties:

   :::image type="content" source="./media/remote-desktop-listener-certificate-configurations/thumbprint-property-windows11.png" alt-text="An example of the certificate thumbprint in the Certificate properties.":::

3. At command prompt, run the following PowerShell command together with the thumbprint value that you obtain in step 2:

   ```console
   Get-WmiObject -class "Win32_TSGeneralSetting" -Namespace root\cimv2\terminalservices | Set-WmiInstance -Arguments @{SSLCertificateSHA1Hash="THUMBPRINT"}
   ```

    The following screenshot shows a successful example:

   :::image type="content" source="./media/remote-desktop-listener-certificate-configurations/successful-example-to-run-powershell-commands.png" alt-text="A successful example of running the PowerShell command together with the thumbprint value that you obtain in step 2." border="false":::

::: zone-end

### [Registry](#tab/registry)

> [!IMPORTANT]
> Follow the steps in this section carefully. Serious problems might occur if you modify the registry incorrectly. Before you modify it, [How to back up and restore the registry in Windows](https://support.microsoft.com/help/322756) in case problems occur.

To configure a certificate by using registry editor, follow these steps:

1. Install a server authentication certificate to the Personal certificate store by using a computer account.

2. Create the following registry value that contains the certificate's SHA1 hash so that you can configure this custom certificate to support TLS instead of using the default self-signed certificate.

   - Registry path: `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp`
   - Value name: **SSLCertificateSHA1Hash**
   - Value type: REG_BINARY
   - Value data: **certificate thumbprint**

    The value should be the thumbprint of the certificate and be separated by comma (,) without any empty spaces. For example, if you were to export that registry key, the **SSLCertificateSHA1Hash** value would be as follows:

    ```reg
    Windows Registry Editor Version 5.00
    
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp]
    "SSLCertificateSHA1Hash"=hex:42,49,e1,6e,0a,f0,a0,2e,63,c4,5c,93,fd,52,ad,09,27,82,1b,01
    ```

3. The Remote Desktop Host Services runs under the NETWORK SERVICE account. Therefore, you have to set the system access control list (SACL) of the key file that is used by RDS to include NETWORK SERVICE together with the **Read** permissions.

   To change the permissions, follow these steps on the Certificates snap-in for the local computer:

   1. Select **Start**, select **Run**, type *mmc*, and then select **OK**.
   2. On the **File** menu, select **Add/Remove Snap-in**.
   3. In the **Add or Remove Snap-ins**  dialog box, on the **Available snap-ins** list, select **Certificates**, and then select **Add**.
   4. In the **Certificates** snap-in dialog box, select **Computer account**, and then select **Next**.
   5. In the **Select Computer** dialog box, select **Local computer: (the computer this console is running on)**, and then select **Finish**.
   6. In the **Add or Remove Snap-ins** dialog box, select **OK**.
   7. In the **Certificates** snap-in, on the console tree, expand **Certificates (Local Computer)**, expand **Personal**, and then select the SSL certificate that you want to use.
   8. Right-click the certificate, select **All Tasks**, and then select **Manage Private Keys**.
   9. In the **Permissions** dialog box, select **Add**, type *NETWORK SERVICE*, select **OK**, select **Read** under the **Allow** check box, and then select **OK**.

### [MMC](#tab/mmc)

::: zone pivot="windows-server-pre-2012"

The Remote Desktop Configuration Manager Microsoft Management Console (MMC) snap-in enables you direct access to the RDP listener. In the snap-in, you can bind a certificate to the listener and in turn, enforce SSL security for the RDP sessions.

::: zone-end
::: zone pivot="windows-server-2012"

The Microsoft Management Console (MMC) method isn't available starting from Windows Server 2012 or Windows Server 2012 R2. However, you can always configure the RDP listener by using WMI or the registry.

::: zone-end
::: zone pivot="windows-11-or-server-2025"

The Microsoft Management Console (MMC) method isn't available starting from Windows Server 2012 or Windows Server 2012 R2. However, you can always configure the RDP listener by using WMI or the registry.

::: zone-end
