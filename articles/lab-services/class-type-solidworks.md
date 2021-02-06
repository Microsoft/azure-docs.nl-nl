---
title: Stel een SOLIDWORKS Lab in voor engineering met Azure Lab Services | Microsoft Docs
description: Meer informatie over het instellen van een Lab voor technische cursussen met behulp van SOLIDWORKS.
author: nicolela
ms.topic: article
ms.date: 06/26/2020
ms.author: nicolela
ms.openlocfilehash: 3a7e8c47977f0518a3a3e9f8a6fd2e57454e1c42
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/06/2021
ms.locfileid: "99626101"
---
# <a name="set-up-a-lab-for-engineering-classes-using-solidworks"></a>Een Lab voor technische klassen instellen met behulp van SOLIDWORKS

[SOLIDWORKS](https://www.solidworks.com/) biedt een 3D-omgeving met computer aided design (CAD) voor het model leren van solide objecten en wordt gebruikt in verschillende soorten technische velden.  Met SOLIDWORKS kunnen technici hun ontwerpen eenvoudig maken, visualiseren, simuleren en documenteren.

Een licentie optie die veel door universiteiten wordt gebruikt, is ' netwerk licenties van SOLIDWORKS '.   Met deze optie delen gebruikers een pool van licenties die worden beheerd door een licentie server.  Dit type licentie wordt ook wel een ' zwevende ' licentie genoemd omdat u slechts voldoende licenties nodig hebt voor het aantal gelijktijdige gebruikers.  Wanneer een gebruiker wordt uitgevoerd met SOLIDWORKS, gaat de licentie terug naar de centraal beheerde licentie groep zodat deze door een andere gebruiker opnieuw kan worden gebruikt.

In dit artikel laten we zien hoe u een klasse instelt die gebruikmaakt van SOLIDWORKS 2019 en netwerk licenties.

## <a name="license-server"></a>Licentie server

Voor SOLIDWORKS-netwerk licenties moet SolidNetWork License Manager zijn geïnstalleerd en geactiveerd op uw licentie server.  Deze licentie server bevindt zich doorgaans in uw on-premises netwerk of in een particulier netwerk in Azure.  Zie voor meer informatie over het instellen van SolidNetWork License Manager op uw server [een licentie Manager installeren en activeren](https://help.solidworks.com/2019/English/Installation/install_guide/t_installing_snl_lic_mgr.htm) in de installatie handleiding voor SOLIDWORKS.  Wanneer u dit instelt, onthoud dan het **poort nummer** en het [**serie nummer**](https://help.solidworks.com/2019/english/installation/install_guide/r_hid_state_serial_number.htm) dat wordt gebruikt, omdat deze in latere stappen nodig zijn.

Nadat de licentie server is ingesteld, moet u het [virtuele netwerk (VNet)](./how-to-connect-peer-virtual-network.md) koppelen aan uw [Lab-account](./tutorial-setup-lab-account.md).  De netwerk peering moet worden uitgevoerd voordat u het Lab maakt, zodat de virtuele machines van het lab toegang hebben tot de licentie server en de andere manier.

> [!NOTE]
> Controleer of de juiste poorten zijn geopend op de firewalls om communicatie tussen de virtuele machines van het lab en de licentie server toe te staan.  Zie bijvoorbeeld de instructies voor het wijzigen van de [computer poorten voor licentie beheer voor Windows Firewall](http://help.solidworks.com/2019/english/installation/install_guide/t_mod_ports_on_lic_mgr_for_firewall.htm) die laten zien hoe u regels voor binnenkomend en uitgaand verkeer toevoegt aan de firewall van de licentie server.  Mogelijk moet u ook poorten openen voor de virtuele lab-machines.  Volg de stappen in het artikel over [firewall instellingen voor Labs](./how-to-configure-firewall-settings.md) voor meer informatie hierover, inclusief het verkrijgen van het open bare IP-adres van het lab.

## <a name="lab-configuration"></a>Lab-configuratie

Als u dit Lab wilt instellen, hebt u een Azure-abonnement en een Lab-account nodig om aan de slag te gaan. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint. Zodra u een Azure-abonnement hebt ontvangen, kunt u een nieuw Lab-account maken in Azure Lab Services. Zie de zelf studie over het [instellen van een Lab-account](./tutorial-setup-lab-account.md)voor meer informatie over het maken van een nieuw Lab-account. U kunt ook een bestaand Lab-account gebruiken.

### <a name="lab-account-settings"></a>Instellingen van Lab-account

Schakel de instellingen die worden beschreven in de onderstaande tabel voor het lab-account in. Voor meer informatie over het inschakelen van Marketplace-installatie kopieën raadpleegt u het artikel over het [opgeven van Marketplace-installatie kopieën die beschikbaar zijn voor Lab-makers](./specify-marketplace-images.md).

| Account instelling Lab | Instructies |
| ------------------- | ------------ |
|Marketplace-installatiekopie| Schakel de Windows 10 Pro-installatie kopie in voor gebruik binnen uw Lab-account.|

> [!NOTE]
> Naast Windows 10 ondersteunt SOLIDWORKS andere versies van Windows.  Raadpleeg de [systeem vereisten voor SOLIDWORKS](https://www.solidworks.com/sw/support/SystemRequirements.html) voor meer informatie.

### <a name="lab-settings"></a>Lab-instellingen

Gebruik de instellingen in de onderstaande tabel bij het instellen van een leslokaal Lab. Zie een leslokaal cursus instellen voor meer informatie over het maken van een leslokaal Lab.

| Lab-instellingen | Waarde/instructies |
| ------------ | ------------------ |
|Grootte van de virtuele machine| **Kleine GPU (visualisatie)**.  Deze VM is het meest geschikt voor externe visualisatie, streaming, games, code ring met behulp van frameworks zoals OpenGL en DirectX.|  
|Installatie kopie van virtuele machine| Windows 10 Pro|

> [!NOTE]
> De grootte van de virtuele machine van **Small GPU (visualisatie)** is zo geconfigureerd dat een hoogwaardige grafische ervaring kan worden uitgevoerd.  Zie het artikel over het [instellen van een Lab met gpu's](./how-to-setup-lab-gpu.md)voor meer informatie over deze grootte van de virtuele machine.

> [!WARNING]
> Vergeet niet om het [virtuele netwerk](./how-to-connect-peer-virtual-network.md) voor het lab-account te koppelen aan het virtuele netwerk van de licentie server **voordat** u het Lab maakt.

## <a name="template-virtual-machine-configuration"></a>Configuratie van de virtuele machine van de sjabloon

De stappen in deze sectie laten zien hoe u de virtuele machine van uw sjabloon kunt instellen door de installatie bestanden van de SOLIDWORKS te downloaden en de client software te installeren:

1. Start de virtuele machine van de sjabloon en maak verbinding met de computer met behulp van RDP.

1. Down load de installatie bestanden voor SOLIDWORKS-client software. U hebt twee opties voor het downloaden:
   - Down load van [SOLIDWORKS Customer Portal](https://login.solidworks.com/nidp/idff/sso?id=cpenglish&sid=1&option=credential&sid=1&target=https%3A%2F%2Fcustomerportal.solidworks.com%2F).
   - Down load uit een directory op een server.  Als u deze optie hebt gebruikt, moet u ervoor zorgen dat de server toegankelijk is vanaf de virtuele machine van de sjabloon.  Deze server kan zich bijvoorbeeld bevinden in hetzelfde virtuele netwerk dat is gekoppeld aan uw Lab-account.
  
    Zie [installatie op afzonderlijke computers in de SolidWorks in de](http://help.solidworks.com/2019/english/Installation/install_guide/c_installing_on_individual_computers.htm?id=fc149e8a968a422a89e2a943265758d3#Pg0) installatie handleiding voor SolidWorks voor meer informatie.

1. Nadat de installatie bestanden zijn gedownload, installeert u de-client software met behulp van SOLIDWORKS Installation Manager. Zie voor meer informatie over [het installeren van een licentie-client in de](http://help.solidworks.com/2019/english/installation/install_guide/t_installing_snl_license_client.htm) installatie handleiding voor SOLIDWORKS.

    > [!NOTE]
    > In het dialoog venster **server toevoegen** wordt u gevraagd om het **poort nummer** dat wordt gebruikt voor uw licentie server en de naam of het IP-adres van de licentie server.

## <a name="cost"></a>Kosten

Laten we een mogelijke schatting van de kosten voor deze klasse beslaan. Deze schatting omvat niet de kosten voor het uitvoeren van de licentie server. We gebruiken een klasse van 25 studenten. Er zijn 20 uur geplande tijd voor de klasse. Daarnaast krijgt elke student tien uur quota voor huis werk of toewijzingen buiten een geplande klasse tijd. De grootte van de virtuele machine die u hebt gekozen, is een **kleine GPU (visualisatie)**. dit zijn 160 Lab-eenheden.

25 studenten \* (20 geplande uren + 10 quotum uren) \* 160 Lab-eenheden * 0,01 USD per uur = 1200,00 USD

>[!IMPORTANT]
> Kosten raming is alleen bedoeld als voor beeld.  Zie [Azure Lab Services prijzen](https://azure.microsoft.com/pricing/details/lab-services/)voor actuele Details over prijzen.  

## <a name="next-steps"></a>Volgende stappen

De volgende stappen zijn gebruikelijk voor het instellen van elk lab.

- [Een sjabloon maken en beheren](how-to-create-manage-template.md)
- [Gebruikers toevoegen](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Quota instellen](how-to-configure-student-usage.md#set-quotas-for-users)
- [Een planning instellen](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [E-mail registratie koppelingen naar studenten](how-to-configure-student-usage.md#send-invitations-to-users)