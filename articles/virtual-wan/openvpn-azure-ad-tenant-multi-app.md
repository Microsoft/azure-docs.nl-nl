---
title: 'Virtuele WAN: Azure AD-Tenant voor verschillende gebruikers groepen: Azure AD-verificatie'
description: Stel een Azure AD-Tenant in voor P2S OpenVPN-verificatie en maak en registreer meerdere apps in azure AD om verschillende gebruikers en groepen toegang te geven.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: alzam
ms.openlocfilehash: e25ef7f55492be4ee491b9ebbbef4aa1eb03c80b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98898084"
---
# <a name="create-an-azure-active-directory-ad-tenant-for-p2s-openvpn-protocol-connections"></a>Een Azure Active Directory-Tenant (AD) maken voor P2S OpenVPN-protocol verbindingen

Wanneer u verbinding maakt met uw VNet, kunt u verificatie op basis van certificaten of RADIUS-verificatie gebruiken. Wanneer u echter het open VPN-protocol gebruikt, kunt u ook Azure Active Directory-verificatie gebruiken. Als u wilt dat een andere set gebruikers verbinding kan maken met verschillende gateways, kunt u meerdere apps registreren in AD en deze koppelen aan verschillende gateways.

Dit artikel helpt u bij het instellen van een Azure AD-Tenant voor P2S OpenVPN-verificatie, en het maken en registreren van meerdere apps in azure AD om verschillende gebruikers en groepen toegang te geven.

> [!NOTE]
> Azure AD-verificatie wordt alleen ondersteund voor OpenVPN- &reg; Protocol verbindingen.
>

[!INCLUDE [create](../../includes/openvpn-azure-ad-tenant-multi-app.md)]

## <a name="6-create-a-new-p2s-configuration"></a><a name="site"></a>6. een nieuwe P2S-configuratie maken

Een P2S-configuratie definieert de parameters om verbinding te maken met externe clients.

1. Stel de volgende variabelen in en vervang waarden naar behoefte voor uw omgeving.

   ```azurepowershell-interactive
   $aadAudience = "00000000-abcd-abcd-abcd-999999999999"
   $aadIssuer = "https://sts.windows.net/00000000-abcd-abcd-abcd-999999999999/"
   $aadTenant = "https://login.microsoftonline.com/00000000-abcd-abcd-abcd-999999999999"    
   ```

2. Voer de volgende opdrachten uit om de configuratie te maken:

   ```azurepowershell-interactive
   $aadConfig = New-AzVpnServerConfiguration -ResourceGroupName <ResourceGroup> -Name newAADConfig -VpnProtocol OpenVPN -VpnAuthenticationType AAD -AadTenant $aadTenant -AadIssuer $aadIssuer -AadAudience $aadAudience -Location westcentralus
   ```

   > [!NOTE]
   > Gebruik de toepassings-ID van de Azure VPN-client niet in de bovenstaande opdrachten: alle gebruikers krijgen toegang tot de gateway. Gebruik de ID van de toepassing (en) die u hebt geregistreerd.

## <a name="7-edit-hub-assignment"></a><a name="hub"></a>7. hub-toewijzing bewerken

1. Navigeer naar de Blade **hubs** onder het virtuele WAN.

2. Selecteer de hub waaraan u de VPN-server configuratie wilt koppelen en klik op het weglatings teken (...).

    ![Scherm afbeelding toont het bewerken van een virtuele hub die is geselecteerd in het menu.](media/openvpn-azure-ad-tenant-multi-app/p2s4.jpg)

3. Klik op **virtuele hub bewerken**.

4. Schakel het selectie vakje een **punt-naar-site-gateway insluiten** in en selecteer de gewenste **Gateway-schaal eenheid** .

    ![Scherm afbeelding toont het dialoog venster Virtuele hub bewerken waarin u uw schaal eenheid voor de gateway kunt selecteren.](media/openvpn-azure-ad-tenant-multi-app/p2s2.jpg)

5. Voer de **adres groep** in van waaruit de VPN-clients IP-adressen worden toegewezen.

6. Klik op **Bevestigen**.

7. De bewerking kan dertig minuten duren.

## <a name="8-download-vpn-profile"></a><a name="device"></a>8. VPN-profiel downloaden

Gebruik het VPN-profiel om uw clients te configureren.

1. Klik op de pagina voor uw virtuele WAN op **VPN-configuraties voor gebruikers**.

2. Klik boven aan de pagina op **VPN-configuratie voor gebruiker downloaden**.

3. Wanneer het bestand gereed is, klikt u op de koppeling om het te downloaden.

4. Gebruik het profielbestand om de VPN-clients te configureren.

5. Pak het gedownloade zip-bestand uit.

6. Blader naar de map ungezipte ' AzureVPN '.

7. Noteer de locatie van het bestand azurevpnconfig.xml. De azurevpnconfig.xml bevat de instelling voor de VPN-verbinding en kan rechtstreeks in de Azure VPN-client toepassing worden geïmporteerd. U kunt dit bestand ook distribueren naar alle gebruikers die verbinding moeten maken via e-mail of een andere manier. De gebruiker heeft geldige Azure AD-referenties nodig om verbinding te kunnen maken.

## <a name="9-configure-user-vpn-clients"></a>9. VPN-clients voor gebruikers configureren

Als u verbinding wilt maken, moet u de Azure VPN-client downloaden en het VPN-client profiel importeren dat in de vorige stappen is gedownload op elke computer die verbinding wil maken met het VNet.

> [!NOTE]
> Azure AD-verificatie wordt alleen ondersteund voor OpenVPN- &reg; Protocol verbindingen.
>

#### <a name="to-download-the-azure-vpn-client"></a>De Azure VPN-client downloaden

Gebruik deze [koppeling](https://go.microsoft.com/fwlink/?linkid=2117554) om de Azure VPN-client te downloaden.

#### <a name="to-import-a-client-profile"></a><a name="import"></a>Een client profiel importeren

1. Selecteer op de pagina **importeren**.

    ![Scherm afbeelding toont importeren geselecteerd in het plus menu.](./media/openvpn-azure-ad-tenant-multi-app/import/import1.jpg)

2. Blader naar het profiel XML-bestand en selecteer het. Selecteer **openen** terwijl het bestand is geselecteerd.

    ![Scherm afbeelding toont een geopend dialoog venster waarin u een bestand kunt selecteren.](./media/openvpn-azure-ad-tenant-multi-app/import/import2.jpg)

3. Geef de naam van het profiel op en selecteer **Opslaan**.

    ![Scherm afbeelding toont de toegevoegde verbindings naam en de knop Opslaan geselecteerd.](./media/openvpn-azure-ad-tenant-multi-app/import/import3.jpg)

4. Selecteer **verbinding maken** om verbinding te maken met het VPN.

    ![Scherm afbeelding toont de knop verbinding maken voor de voor de verbinding die u zojuist hebt gemaakt.](./media/openvpn-azure-ad-tenant-multi-app/import/import4.jpg)

5. Zodra de verbinding is gemaakt, wordt het pictogram groen en vervolgens **verbonden**.

    ![Scherm afbeelding toont de verbinding met een verbonden status met de optie voor het verbreken van de koppeling.](./media/openvpn-azure-ad-tenant-multi-app/import/import5.jpg)

#### <a name="to-delete-a-client-profile"></a><a name="delete"></a>Een client profiel verwijderen

1. Selecteer het beletsel teken (...) naast het client profiel dat u wilt verwijderen. Selecteer vervolgens **verwijderen**.

    ![Scherm afbeelding toont verwijderen geselecteerd in het menu.](./media/openvpn-azure-ad-tenant-multi-app/delete/delete1.jpg)

2. Selecteer **verwijderen** om te verwijderen.

    ![Scherm afbeelding toont een bevestigings dialoogvenster met de optie om te verwijderen of te annuleren.](./media/openvpn-azure-ad-tenant-multi-app/delete/delete2.jpg)

#### <a name="to-diagnose-connection-issues"></a><a name="diagnose"></a>Verbindings problemen vaststellen

1. Als u verbindings problemen wilt vaststellen, kunt u het hulp programma voor **diagnose** gebruiken. Selecteer het beletsel teken (...) naast de VPN-verbinding die u wilt diagnosticeren om het menu weer te geven. Selecteer vervolgens **diagnose**.

    ![Scherm opname bevat de optie diagnose geselecteerd in het menu.](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose1.jpg)

2. Selecteer op de pagina **Eigenschappen van verbinding** de optie **diagnose uitvoeren**.

    ![Scherm afbeelding toont de knop diagnose uitvoeren voor een verbinding.](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose2.jpg)

3. Meld u aan met uw referenties.

    ![problemen oplossen 3](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose3.jpg)

4. Bekijk de resultaten van de diagnose.

    ![Scherm afbeelding toont de knop diagnose uitvoeren voor een verbinding.](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose2.jpg)

3. Meld u aan met uw referenties.

    ![Scherm afbeelding toont het dialoog venster Aanmelden voor deze actie.](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose3.jpg)

4. Bekijk de resultaten van de diagnose.

    ![Scherm afbeelding toont de resultaten van de diagnose.](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose4.jpg)

## <a name="10-view-your-virtual-wan"></a><a name="viewwan"></a>10. uw virtuele WAN weer geven

1. Navigeer naar uw virtuele WAN.

2. Op de pagina Overzicht vertegenwoordigt elk punt op de kaart een hub.

3. In de sectie Hubs en verbindingen vindt u informatie over de hubstatus, site, regio, VPN-verbindingsstatus en verzonden en ontvangen bytes.

## <a name="clean-up-resources"></a><a name="cleanup"></a>Resources opschonen

U kunt de opdracht [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) gebruiken om de resourcegroep en alle resources die deze bevat te verwijderen, wanneer u deze niet meer nodig hebt. Vervangen 'myResourceGroup' door de naam van uw resourcegroep en voer de volgende PowerShell-opdracht uit:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over Virtual WAN de pagina [Overzicht van Virtual WAN](virtual-wan-about.md).
