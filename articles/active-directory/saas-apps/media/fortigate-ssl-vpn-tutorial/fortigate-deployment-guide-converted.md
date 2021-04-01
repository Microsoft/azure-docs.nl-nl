---
title: Implementatiehandleiding voor FortiGate | Microsoft Docs
description: De Fortinet FortiGate-firewall van de volgende generatie instellen en gebruiken.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/30/2020
ms.author: jeedes
ms.openlocfilehash: cdaa6a9601452100ab90ef8b0f2191002f256b74
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95025467"
---
# <a name="fortigate-azure-virtual-machine-deployment-guide"></a>Implementatiehandleiding voor virtuele Azure-machines met Fortigate

Met deze implementatiehandleiding leert u de Fortinet FortiGate-firewall van de volgende generatie instellen en gebruiken. Dit product wordt geïmplementeerd als een virtuele Azure-machine. Daarnaast configureert u de FortiGate SSL VPN Azure AD Gallery-app om VPN-verificatie via Azure Active Directory mogelijk te maken.

## <a name="redeem-the-fortigate-license"></a>De FortiGate-licentie inwisselen

Het product Fortinet FortiGate Next-Generation Firewall is beschikbaar als een virtuele machine in Azure IaaS (infrastructuur als een dienst). Er zijn twee licentiemodi voor deze virtuele machine: betalen per gebruik en eigen licentie (BYOL).

Als u een FortiGate-licentie hebt aangeschaft in Fortinet voor gebruik met de BYOL-implementatieoptie voor virtuele machines, kunt u deze inwisselen via de productactiveringspagina van Fortinet: https://support.fortinet.com. Het resulterende licentiebestand heeft de bestandsextensie .lic.

## <a name="download-firmware"></a>Firmware downloaden

Op het moment van schrijven wordt de Fortinet FortiGate Azure-VM geleverd zonder de firmwareversie die nodig is voor SAML-verificatie. U kunt de nieuwste versie downloaden op de site van Fortinet.

1. Meld u aan op https://support.fortinet.com/.
2. Ga naar **Download** > **Firmware Images**.
3. Selecteer **Download** rechts van **Release Notes**.
4. Selecteer **v6.00** > **6.4** > **6.4.2**.
5. Download **FGT_VM64_AZURE-v6-build1723-FORTINET.out** door in dezelfde rij de link **HTTPS** te selecteren.
6. Sla het bestand op voor later.

## <a name="deploy-the-fortigate-vm"></a>De FortiGate-VM implementeren

1. Ga naar Azure Portal en meld u aan bij het abonnement waarin u de virtuele machine van FortiGate wilt implementeren.
2. Maak een nieuwe resourcegroep of open de resourcegroep waarin u de virtuele machine van FortiGate wilt implementeren.
3. Selecteer **Toevoegen**.
4. Voer *Forti* in bij **Marktplaats doorzoeken**. Selecteer **Fortinet FortiGate Next-Generation Firewall**
5. Selecteer het software-abonnement (eigen licentie indien u die hebt of anders betalen per gebruik). Selecteer **Maken**.
6. Vul de VM-configuratie in.

    ![Schermafbeelding van Een virtuele machine maken](virtual-machine.png)

7. Stel het **Authentication type** in op **Password** en geef beheerdersreferenties in voor de virtuele machine.
8. Selecteer **Beoordelen en maken** > **Maken**.
9. Wacht totdat de implementatie van de VM is voltooid.


### <a name="set-a-static-public-ip-address-and-assign-a-fully-qualified-domain-name"></a>Een statisch openbaar IP-adres instellen en een FQDN toewijzen

Voor een consistente gebruikerservaring  wijst u het openbare IP-adres voor de FortiGate-VM statisch toe. Wijs het daarnaast toe aan een FQDN (Fully Qualified Domain Name).

1. Ga naar Azure Portal en open de instellingen voor de FortiGate-VM.
2. Selecteer op het scherm **Overview** op het openbare IP-adres.

    ![Schermopname van de Forti Gate SSL VPN.](public-ip-address.png)

3. Selecteer **Static** > **Save**.

Als u eigenaar bent van een openbaar routeerbare domeinnaam voor de omgeving waarin de FortiGate-VM wordt geïmplementeerd, maakt u een hostrecord (A-record) voor de VM. Deze record wordt toegewezen aan het voorafgaande openbare IP-adres dat statisch is toegewezen.

### <a name="create-a-new-inbound-network-security-group-rule-for-tcp-port-8443"></a>Een nieuwe regel voor inkomend verkeer voor een netwerkbeveiligingsgroep maken voor TCP-poort 8443

1. Ga naar Azure Portal en open de instellingen voor de FortiGate-VM.
2. Selecteer **Networking** in het menu aan de linkerkant. De netwerkinterface wordt weergegeven evenals de regels voor binnenkomende poorten.
3. Selecteer **Add inbound port rule**.
4. Maak een nieuwe regel voor binnenkomende poort TCP 8443.

    ![Schermafbeelding van Add inbound security rule.](port-rule.png)

5. Selecteer **Toevoegen**.

## <a name="create-a-second-virtual-nic-for-the-vm"></a>Een tweede virtuele NIC voor de VM maken

Voor interne resources die beschikbaar moeten worden gemaakt voor gebruikers, moet een tweede virtuele NIC worden toegevoegd aan de FortiGate-VM. Het virtuele netwerk in Azure waarop de virtuele NIC zich bevindt, moet een routeerbare verbinding met die interne resources hebben.

1. Ga naar Azure Portal en open de instellingen voor de FortiGate-VM.
2. Als de FortiGate-VM nog niet is gestopt, selecteert u **Stoppen** en wacht u totdat de VM is afgesloten.
3. Selecteer **Networking** in het menu aan de linkerkant.
4. Selecteer **Netwerkinterface koppelen**.
5. Selecteer **Netwerkinterface maken en koppelen**.
6. Configureer de eigenschappen voor de nieuwe netwerkinterface en selecteer vervolgens **Maken**.

    ![Schermopname van Netwerkinterface maken.](new-network-interface.png)

7. Start de FortiGate-VM.


## <a name="configure-the-fortigate-vm"></a>De FortiGate-VM configureren

De volgende secties helpen u bij het instellen van de FortiGate-VM.

### <a name="install-the-license"></a>De licentie installeren

1. Ga naar `https://<address>`. waarbij `<address>` de FQDN is of het openbare IP-adres dat is toegewezen aan de FortiGate-VM.

2. Negeer eventuele certificaatfouten.
3. Meld u aan met de beheerdersreferenties die zijn verstrekt tijdens de implementatie van de FortiGate-VM.
4. Als voor de implementatie een eigen licentie nodig is, wordt u gevraagd een licentie te uploaden. Selecteer het licentiebestand dat u eerder hebt gemaakt en upload het. Selecteer **OK** en start de FortiGate-VM opnieuw op.

    ![Schermopname van de FortiGate VM-licentie](license.png)

5. Nadat de VM opnieuw is opgestart, meldt u zich opnieuw aan met de referenties van de beheerder om de licentie te valideren

### <a name="update-firmware"></a>Firmware bijwerken

1. Ga naar `https://<address>`. waarbij `<address>` de FQDN is of het openbare IP-adres dat is toegewezen aan de FortiGate-VM.

2. Negeer eventuele certificaatfouten.
3. Meld u aan met de beheerdersreferenties die zijn verstrekt tijdens de implementatie van de FortiGate-VM.
4. Selecteer **System** > **Firmware** in het menu aan de linkerkant.
5. Selecteer op de pagina **Firmware Management** op **Browse** en selecteer het firmwarebestand dat u eerder hebt gedownload.
6. Negeer de waarschuwing en selecteer **Backup config and upgrade**.

    ![Schermafbeelding van Firmware Management.](backup-configure-upgrade.png)

7. Selecteer **Doorgaan**.
8. Wanneer u wordt gevraagd om de FortiGate-configuratie op te slaan (als een .conf-bestand), selecteert u **Save**.
9. Wacht tot de firmware is geüpload en toegepast. Wacht totdat de FortiGate-VM opnieuw is opgestart.
10. Als de FortiGate-VM opnieuw is opgestart, meldt u zich opnieuw aan met de referenties van de beheerder.
11. Wanneer u wordt gevraagd het dashboard in te stellen, selecteert u **Later**.
12. Wanneer de video start, selecteert u **OK**.

### <a name="change-the-management-port-to-tcp-8443"></a>De beheerpoort wijzigen in TCP 8443

1. Ga naar `https://<address>`. waarbij `<address>` de FQDN is of het openbare IP-adres dat is toegewezen aan de FortiGate-VM.

2. Negeer eventuele certificaatfouten.
3. Meld u aan met de beheerdersreferenties die zijn verstrekt tijdens de implementatie van de FortiGate-VM.
4. Selecteer **System** in het menu aan de linkerkant.
5. Wijzig onder **Administration Settings** de HTTPS-poort in **8443** en selecteer **Apply**.
6. Als de wijziging is toegepast, probeert de browser de beheerpagina opnieuw te laden maar dit lukt niet. Het adres van de beheerpagina is vanaf nu namelijk `https://<address>:8443`.

    ![Schermafbeelding van Upload Remote Certificate.](certificate.png)

### <a name="upload-the-azure-ad-saml-signing-certificate"></a>Het SAML-handtekeningcertificaat van Azure AD uploaden

1. Ga naar `https://<address>:8443`. waarbij `<address>` de FQDN is of het openbare IP-adres dat is toegewezen aan de FortiGate-VM.

2. Negeer eventuele certificaatfouten.
3. Meld u aan met de beheerdersreferenties die zijn verstrekt tijdens de implementatie van de FortiGate-VM.
4. Selecteer **System** > **Certificates** in het menu aan de linkerkant
5. Selecteer **Import** > **Remote Certificate**.
6. Blader naar het certificaat dat u hebt gedownload uit de implementatie van de aangepaste FortiGate-app in de Azure-tenant. Selecteer het en vervolgens **OK**.

### <a name="upload-and-configure-a-custom-ssl-certificate"></a>Een aangepast SSL-certificaat uploaden en configureren

U kunt de FortiGate-VM configureren met uw eigen SSL-certificaat dat ondersteuning biedt voor de FQDN die u gebruikt. Als u toegang hebt tot een SSL-certificaat dat is verpakt met de persoonlijke sleutel in PFX-indeling, kan dit voor dit doel worden gebruikt.

1. Ga naar `https://<address>:8443`. waarbij `<address>` de FQDN is of het openbare IP-adres dat is toegewezen aan de FortiGate-VM.

2. Negeer eventuele certificaatfouten.
3. Meld u aan met de beheerdersreferenties die zijn verstrekt tijdens de implementatie van de FortiGate-VM.
4. Selecteer **System** > **Certificates** in het menu aan de linkerkant
5. Selecteer **Import** > **Local Certificate** > **PKCS #12 Certificate**.
6. Blader naar het PFX-bestand dat het SSL-certificaat en de persoonlijke sleutel bevat.
7. Geef het .PFX-wachtwoord en een beschrijvende naam voor het certificaat op. Selecteer vervolgens **OK**.
8. Selecteer **System** > **Settings** in het menu aan de linkerkant.
9. Open onder **Administration Settings** de lijst naast **HTTPS server certificate** en selecteer het SSL-certificaat dat u eerder hebt geïmporteerd.
10. Selecteer **Toepassen**.
11. Sluit het browservenster en ga naar `https://<address>:8443`.
12. Meld u aan met de FortiGate-beheerdersreferenties. U ziet nu het juiste SSL-certificaat dat wordt gebruikt.

### <a name="configure-authentication-timeout"></a>Time-out voor verificatie configureren

1. Ga naar Azure Portal en open de instellingen voor de FortiGate-VM.
2. Selecteer **Serial Console** in het menu aan de linkerkant.
3. Meld u aan bij de console met de beheerdersreferenties voor de FortiGate-VM.
4. Voer de volgende opdrachten uit in de console:

    ```
    config system global
    set remoteauthtimeout 60
    end
    ```

### <a name="ensure-network-interfaces-are-obtaining-ip-addresses"></a>Controleren of netwerkinterfaces IP-adressen verkrijgen

1. Ga naar `https://<address>:8443`. waarbij `<address>` de FQDN is of het openbare IP-adres dat is toegewezen aan de FortiGate-VM.

2. Meld u aan met de beheerdersreferenties die zijn verstrekt tijdens de implementatie van de FortiGate-VM.
3. Selecteer **Networking** in het menu aan de linkerkant.
4. Selecteer **Interfaces** onder Netwerk.
5. Bekijk port1 (externe interface) en port2 (interne interface) om te controleren of ze een IP-adres verkrijgen van het juiste Azure-subnet.
    a. Als een van beide poorten geen IP-adres van het subnet ontvangt (via DHCP), klikt u met de rechtermuisknop op de poort en selecteert u **Bewerken**.
    b. Zorg ervoor dat **DHCP** is geselecteerd naast de adresseringsmodus.
    c. Selecteer **OK**.

    ![Schermopname van Netwerkinterface-adressering.](addressing.png)

### <a name="ensure-fortigate-vm-has-correct-route-to-on-premises-corporate-resources"></a>De juiste route van de FortiGate-VM naar on-premises bedrijfsresources instellen

Multi-homed Azure-VM's hebben alle netwerkinterfaces in hetzelfde virtuele netwerk (maar mogelijk afzonderlijke subnetten). Dit betekent vaak dat beide netwerkinterfaces een verbinding hebben met de on-premises bedrijfsresources die worden gepubliceerd via FortiGate. Daarom is het noodzakelijk om aangepaste routevermeldingen te maken zodat uitgaand verkeer vanaf de juiste interface wordt toegestaan voor aanvragen voor on-premises bedrijfsresources.

1. Ga naar `https://<address>:8443`. waarbij `<address>` de FQDN is of het openbare IP-adres dat is toegewezen aan de FortiGate-VM.

2. Meld u aan met de beheerdersreferenties die zijn verstrekt tijdens de implementatie van de FortiGate-VM.
3. Selecteer **Networking** in het menu aan de linkerkant.
4. Selecteer onder Netwerk de optie **Statische routes**.
5. Selecteer **Nieuwe maken**.
6. Selecteer naast Doel de optie **Subnet**.
7. Geef onder Subnet op waar de on-premises bedrijfsresources zich bevinden (bijvoorbeeld 10.1.0.0/255.255.255.0)
8. Geef naast het gatewayadres de gateway op het Azure-subnet op waarmee port2 is verbonden (dit eindigt meestal op een 1, bijvoorbeeld 10.6.1.1)
9. Selecteer naast Interface de interne netwerkinterface, port2
10. Selecteer **OK**.

    ![Schermopname van het configureren van een route.](route.png)

## <a name="configure-fortigate-ssl-vpn"></a>FortiGate SSL VPN configureren

Voer de stappen uit die worden beschreven in https://docs.microsoft.com/azure/active-directory/saas-apps/fortigate-ssl-vpn-tutorial
