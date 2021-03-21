---
title: 'Azure AD-verificatie configureren voor de VPN-verbinding van de gebruiker: Virtual WAN'
description: Meer informatie over het configureren van Azure Active Directory-verificatie voor gebruikers-VPN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 10/14/2020
ms.author: alzam
ms.openlocfilehash: 9cc68eb60096c4431acfc988c87ca9bf99f1f045
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93043404"
---
# <a name="configure-azure-active-directory-authentication-for-user-vpn"></a>Azure Active Directory-verificatie voor gebruikers-VPN configureren

In dit artikel wordt beschreven hoe u Azure AD-verificatie configureert voor gebruikers-VPN in een virtueel WAN om verbinding te maken met uw resources in azure via een OpenVPN-VPN-verbinding. Azure Active Directory-verificatie is alleen beschikbaar voor gateways met behulp van OpenVPN-protocol en clients met Windows.

Voor dit soort verbinding moet een client op de clientcomputer worden geconfigureerd. Zie voor meer informatie over Virtual WAN het [Overzicht van Virtual WAN](virtual-wan-about.md).

In dit artikel leert u het volgende:

* Een virtueel WAN maken
* Een virtuele hub maken
* Een VPN-gebruikers configuratie maken
* Een VPN-profiel voor virtuele WAN-gebruikers downloaden
* VPN-configuratie voor gebruiker Toep assen op een virtuele hub
* Een VNet verbinden met een virtuele hub
* De configuratie van de gebruiker VPN-client downloaden en Toep assen
* Uw virtuele WAN weer geven

![Virtual WAN-diagram](./media/virtual-wan-about/virtualwanp2s.png)

## <a name="before-you-begin"></a>Voordat u begint

Controleer voordat u met de configuratie begint of u aan de volgende criteria hebt voldaan:

* U hebt een virtueel netwerk waarmee u verbinding wilt maken. Controleer of geen van de subnetten van uw on-premises netwerken overlapt met de virtuele netwerken waarmee u verbinding wilt maken. Zie de [snelstart](../virtual-network/quick-create-portal.md) als u een virtueel netwerk in de Azure-portal wilt maken.

* Uw virtuele netwerk heeft geen virtuele netwerkgateways. Als uw virtuele netwerk een gateway heeft (VPN of ExpressRoute), moet u alle gateways verwijderen. Voor deze configuratie moeten virtuele netwerken in plaats daarvan zijn verbonden met de Virtual WAN-hubgateway.

* Zorg dat u een IP-adresbereik krijgt voor uw hubregio. De hub is een virtueel netwerk dat wordt gemaakt en gebruikt door Virtual WAN. Het adresbereik dat u voor de hub opgeeft mag niet overlappen met een van de bestaande virtuele netwerken waarmee u verbinding wilt maken. Dit bereik mag ook niet overlappen met de adresbereiken waarmee u on-premises verbinding wilt maken. Als u de IP-adresbereiken in uw on-premises netwerkconfiguratie niet kent, moet u contact opnemen met iemand die deze gegevens kan verstrekken.

* Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan.

## <a name="create-a-virtual-wan"></a><a name="wan"></a>Een virtueel WAN maken

Open een browser, ga naar [Azure Portal](https://portal.azure.com) en meld u aan met uw Azure-account.

1. Ga naar de pagina Virtual WAN. Klik in de portal op **+Een resource maken**. Typ **Virtual WAN** in het zoekvak en selecteer Enter.
2. Selecteer **Virtual WAN** uit de resultaten. Klik op de pagina Virtual WAN op **Maken** om de pagina WAN maken te openen.
3. Vul op de pagina **Wan maken**, op het tabblad **Basisprincipes**, de volgende velden in:

   ![Virtuele WAN](./media/virtual-wan-point-to-site-azure-ad/vwan.png)

   * **Abonnement** - selecteer het abonnement dat u wilt gebruiken.
   * **Resourcegroep** - maak een nieuwe resourcegroep of gebruik een bestaande.
   * **Locatie van de resourcegroep** - kies een resourcelocatie uit de vervolgkeuzelijst. Een WAN een globale resource en bevindt zich niet in een bepaalde regio. U moet echter een regio selecteren om de WAN-resource die u maakt eenvoudiger te kunnen beheren en vinden.
   * **Naam**, typ de naam die u voor uw WAN hebt gekozen.
   * **Type:** Standaard. Als u een Basic-WAN maakt, kunt u alleen een Basic-hub maken. Basic-hubs zijn alleen geschikt voor VPN-verbindingen tussen sites.
4. Nadat u klaar bent met het invullen van de velden, selecteert u **+Maken**.
5. Nadat de validatie is geslaagd, selecteert u **maken** om het virtuele WAN te maken.

## <a name="create-an-empty-virtual-hub"></a><a name="site"></a>Een lege virtuele hub maken

1. Onder uw virtuele WAN selecteert u hubs en klikt u op **+ nieuwe hub**.

   ![Scherm afbeelding toont het dialoog venster configuratie van hubs waarvoor nieuwe hub is geselecteerd.](media/virtual-wan-point-to-site-azure-ad/hub1.jpg)
2. Vul de volgende velden in op de pagina virtuele hub maken.

   **Regio**, selecteer de regio waarin u de virtuele hub wilt implementeren.

   **Naam**, typ de naam die u voor uw virtuele hub hebt gekozen.

   **Privé-adresruimte hub**, het adresbereik van de hub in CIDR-notatie.

   ![Scherm afbeelding toont het deel venster Virtuele hub maken waarin u waarden kunt invoeren.](media/virtual-wan-point-to-site-azure-ad/hub2.jpg)  
3. Klik op **Controleren + maken**.
4. Klik op de pagina **door gegeven validatie** op **maken**.

## <a name="create-a-new-user-vpn-configuration"></a><a name="site"></a>Een nieuwe gebruiker VPN-configuratie maken

Een VPN-gebruikers configuratie definieert de para meters voor het verbinden van externe clients.

1. Onder uw virtuele WAN selecteert u **VPN-configuraties voor gebruikers**.

   ![Scherm afbeelding toont het menu-item voor de gebruiker-V P N-configuraties geselecteerd.](media/virtual-wan-point-to-site-azure-ad/aadportal1.jpg)

2. Klik op **+ VPN-configuratie voor gebruiker maken**.

   ![Scherm afbeelding toont de configuratie koppeling gebruiker V P N maken.](media/virtual-wan-point-to-site-azure-ad/aadportal2.jpg)

3. Voer de gegevens in en klik op **maken**.

   * **Configuratie naam** : Voer de naam in die u wilt aanroepen van de VPN-gebruikers configuratie.
   * **Tunnel Type** : Selecteer openvpn.
   * **Verificatie methode** : Selecteer Azure Active Directory.
   * **Doel groep** : Typ de toepassings-id van de [Azure VPN](openvpn-azure-ad-tenant.md) Enter prise-toepassing die in uw Azure AD-Tenant is geregistreerd. 
   * **Verlener** - `https://sts.windows.net/<your Directory ID>/`
   * **AAD-Tenant** - `https://login.microsoftonline.com/<your Directory ID>`
  
   ![Scherm afbeelding toont het configuratie deel venster nieuwe gebruiker maken V P N, waarin u de waarden kunt invoeren.](media/virtual-wan-point-to-site-azure-ad/aadportal3.jpg)

## <a name="edit-hub-assignment"></a><a name="hub"></a>Hubtoewijzing bewerken

1. Navigeer naar de Blade **hubs** onder het virtuele WAN.
2. Selecteer de hub waaraan u de VPN-server configuratie wilt koppelen en klik op het weglatings teken (...).

   ![Scherm afbeelding toont het bewerken van een virtuele hub die is geselecteerd in het menu.](media/virtual-wan-point-to-site-azure-ad/p2s4.jpg)
3. Klik op **virtuele hub bewerken**.
4. Schakel het selectie vakje een **punt-naar-site-gateway insluiten** in en selecteer de gewenste **Gateway-schaal eenheid** .

   ![Scherm afbeelding toont het dialoog venster Virtuele hub bewerken waarin u uw schaal eenheid voor de gateway kunt selecteren.](media/virtual-wan-point-to-site-azure-ad/p2s2.jpg)
5. Voer de **adres groep** in van waaruit de VPN-clients IP-adressen worden toegewezen.
6. Klik op **Bevestigen**.
7. Het kan tot 30 minuten duren voordat de bewerking is voltooid.

## <a name="download-user-vpn-profile"></a><a name="device"></a>VPN-gebruikers profiel downloaden

Gebruik het VPN-profiel om uw clients te configureren.

1. Klik op de pagina voor uw virtuele WAN op **VPN-configuraties voor gebruikers**.
2. Klik boven aan de pagina op **VPN-configuratie voor gebruiker downloaden**.
3. Wanneer het bestand gereed is, klikt u op de koppeling om het te downloaden.
4. Gebruik het profielbestand om de VPN-clients te configureren.

## <a name="configure-user-vpn-clients"></a>VPN-clients voor gebruikers configureren

Als u verbinding wilt maken, moet u de Azure VPN-client downloaden en het VPN-client profiel importeren dat in de vorige stappen is gedownload op elke computer die verbinding wil maken met het VNet.

> [!NOTE]
> Azure AD-verificatie wordt alleen ondersteund voor OpenVPN- &reg; Protocol verbindingen.
>

#### <a name="to-download-the-azure-vpn-client"></a>De Azure VPN-client downloaden

Gebruik deze [koppeling](https://www.microsoft.com/p/azure-vpn-client-preview/9np355qt2sqb?rtc=1&activetab=pivot:overviewtab) om de Azure VPN-client te downloaden.

#### <a name="to-import-a-client-profile"></a><a name="import"></a>Een client profiel importeren

1. Selecteer op de pagina **importeren**.

    ![Scherm afbeelding toont importeren geselecteerd in het plus menu.](./media/virtual-wan-point-to-site-azure-ad/import/import1.jpg)

2. Blader naar het profiel XML-bestand en selecteer het. Selecteer **openen** terwijl het bestand is geselecteerd.

    ![Scherm afbeelding toont een geopend dialoog venster waarin u een bestand kunt selecteren.](./media/virtual-wan-point-to-site-azure-ad/import/import2.jpg)

3. Geef de naam van het profiel op en selecteer **Opslaan**.

    ![Scherm afbeelding toont de toegevoegde verbindings naam en de knop Opslaan geselecteerd.](./media/virtual-wan-point-to-site-azure-ad/import/import3.jpg)

4. Selecteer **verbinding maken** om verbinding te maken met het VPN.

    ![Scherm afbeelding toont de knop verbinding maken voor de voor de verbinding die u zojuist hebt gemaakt.](./media/virtual-wan-point-to-site-azure-ad/import/import4.jpg)

5. Zodra de verbinding is gemaakt, wordt het pictogram groen en vervolgens **verbonden**.

    ![Scherm afbeelding toont de verbinding met een verbonden status met de optie voor het verbreken van de koppeling.](./media/virtual-wan-point-to-site-azure-ad/import/import5.jpg)

#### <a name="to-delete-a-client-profile"></a><a name="delete"></a>Een client profiel verwijderen

1. Selecteer het beletsel teken (...) naast het client profiel dat u wilt verwijderen. Selecteer vervolgens **verwijderen**.

    ![Scherm afbeelding toont verwijderen geselecteerd in het menu.](./media/virtual-wan-point-to-site-azure-ad/delete/delete1.jpg)

2. Selecteer **verwijderen** om te verwijderen.

    ![Scherm afbeelding toont een bevestigings dialoogvenster met de optie om te verwijderen of te annuleren.](./media/virtual-wan-point-to-site-azure-ad/delete/delete2.jpg)

#### <a name="diagnose-connection-issues"></a><a name="diagnose"></a>Problemen met de verbinding vaststellen

1. Als u verbindings problemen wilt vaststellen, kunt u het hulp programma voor **diagnose** gebruiken. Selecteer het beletsel teken (...) naast de VPN-verbinding die u wilt diagnosticeren om het menu weer te geven. Selecteer vervolgens **diagnose**.

    ![Scherm opname bevat de optie diagnose geselecteerd in het menu.](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose1.jpg)

2. Selecteer op de pagina **Eigenschappen van verbinding** de optie **diagnose uitvoeren**.

    ![Scherm afbeelding toont de knop diagnose uitvoeren voor een verbinding.](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose2.jpg)

3. Meld u aan met uw referenties.

    ![Scherm afbeelding toont het dialoog venster Aanmelden voor deze actie.](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose3.jpg)

4. Bekijk de resultaten van de diagnose.

    ![Scherm afbeelding toont de resultaten van de diagnose.](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose4.jpg)

## <a name="view-your-virtual-wan"></a><a name="viewwan"></a>Uw virtuele WAN weergeven

1. Navigeer naar uw virtuele WAN.
2. Op de pagina Overzicht vertegenwoordigt elk punt op de kaart een hub.
3. In de sectie Hubs en verbindingen vindt u informatie over de hubstatus, site, regio, VPN-verbindingsstatus en verzonden en ontvangen bytes.

## <a name="clean-up-resources"></a><a name="cleanup"></a>Resources opschonen

Wanneer u deze resources niet meer nodig hebt, kunt u [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) gebruiken om de resource groep en alle resources die deze bevat, te verwijderen. Vervangen 'myResourceGroup' door de naam van uw resourcegroep en voer de volgende PowerShell-opdracht uit:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over Virtual WAN de pagina [Overzicht van Virtual WAN](virtual-wan-about.md).
