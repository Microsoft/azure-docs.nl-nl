---
title: Bestaande Windows Virtual Desktop (klassieke) hostgroep uitbreiden met nieuwe sessie hosts-Azure
description: Een bestaande hostgroep uitbreiden met nieuwe sessie-hosts in virtueel bureau blad (klassiek) van Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 03/31/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: f82b831d887f0ebdd659167935f2134583b3bb87
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106551948"
---
# <a name="expand-an-existing-host-pool-with-new-session-hosts-in-windows-virtual-desktop-classic"></a>Een bestaande hostgroep uitbreiden met nieuwe sessie-hosts in virtueel bureau blad van Windows (klassiek)

>[!IMPORTANT]
>Deze inhoud is van toepassing op Windows Virtual Desktop (klassiek), dat geen ondersteuning biedt voor Azure Resource Manager Windows Virtual Desktop-objecten. Raadpleeg [dit artikel](../expand-existing-host-pool.md) als u Azure Resource Manager Windows Virtual Desktop-objecten probeert te beheren.

Wanneer u het gebruik in uw hostgroep wilt verbeteren, moet u mogelijk uw bestaande hostgroep uitbreiden met nieuwe sessie-hosts om de nieuwe belasting af te handelen.

In dit artikel wordt uitgelegd hoe u een bestaande hostgroep kunt uitbreiden met nieuwe sessie-hosts.

## <a name="what-you-need-to-expand-the-host-pool"></a>Wat u nodig hebt om de hostgroep uit te breiden

Voordat u begint, moet u ervoor zorgen dat u een hostgroep en virtuele machines (Vm's) voor de host hebt gemaakt met behulp van een van de volgende methoden:

- [Azure Marketplace-aanbieding](create-host-pools-azure-marketplace-2019.md)
- [GitHub-sjabloon Azure Resource Manager](create-host-pools-arm-template.md)
- [Een hostpool maken met PowerShell](create-host-pools-powershell-2019.md)

U hebt ook de volgende informatie nodig bij het maken van de hostgroep en de Vm's voor de sessiehost:

- VM-grootte, installatie kopie en naam voorvoegsel
- Referenties voor domein deelname en Windows Virtual Desktop Tenant beheerder
- Naam van het virtuele netwerk en het subnet

De volgende drie secties zijn drie methoden die u kunt gebruiken om de hostgroep uit te breiden. U kunt dit doen met het implementatie programma waarmee u vertrouwd bent.

>[!NOTE]
>Tijdens de implementatie fase worden fout berichten weer geven voor de vorige VM-bronnen van de sessiehost als deze momenteel worden afgesloten. Deze fouten doen zich voor omdat Azure de Power shell DSC-uitbrei ding niet kan uitvoeren om te valideren dat de host-Vm's op de juiste manier zijn geregistreerd bij uw bestaande hostgroep. De sessiehost waarvan de naam eindigt op '-0 ' moet worden uitgevoerd. u kunt deze fouten echter negeren voor andere sessie-hosts, of u kunt de fouten voor komen door alle virtuele machines van de host in de bestaande hostgroep te starten voordat u het implementatie proces start.

## <a name="redeploy-from-azure"></a>Opnieuw implementeren vanuit Azure

Als u al een hostgroep en host-Vm's hebt gemaakt met behulp van de [Azure Marketplace-aanbieding](create-host-pools-azure-marketplace-2019.md) of de [github Azure Resource Manager sjabloon](create-host-pools-arm-template.md), kunt u dezelfde sjabloon opnieuw implementeren vanuit de Azure Portal. Als de sjabloon opnieuw wordt geïmplementeerd, worden alle gegevens die u in de oorspronkelijke sjabloon hebt ingevoerd, behalve voor wacht woorden, automatisch opnieuw ingevoerd.

U kunt als volgt de Azure Resource Manager-sjabloon opnieuw implementeren om een hostgroep uit te breiden:

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Zoek in de zoek balk aan de bovenkant van de Azure Portal naar **resource groepen** en selecteer het item onder **Services**.
3. Zoek en selecteer de resource groep die u hebt gemaakt tijdens het maken van de hostgroep.
4. Selecteer **implementaties** in het deel venster aan de linkerkant van de browser.
5. Selecteer de juiste implementatie voor het maken van uw hostgroep:
     - Als u de oorspronkelijke hostgroep hebt gemaakt met de Azure Marketplace-aanbieding, selecteert u de implementatie die begint met **RDS. WVD-Provision-host-pool**.
     - Als u de oorspronkelijke hostgroep hebt gemaakt met de GitHub-sjabloon Azure Resource Manager, selecteert u de implementatie met de naam **micro soft. Temp late**.
6. Selecteer opnieuw **implementeren**.

     >[!NOTE]
     >Als de sjabloon niet automatisch opnieuw wordt geïmplementeerd wanneer u opnieuw **implementeren** selecteert, selecteert u **sjabloon** in het deel venster aan de linkerkant van uw browser en selecteert u vervolgens **implementeren**.

7. Selecteer de resource groep die de huidige host-Vm's in de bestaande hostgroep bevat.

     >[!NOTE]
     >Als er een fout bericht wordt weer gegeven waarin u wordt aangegeven dat u een andere resource groep wilt selecteren, terwijl u de naam juist hebt ingevoerd, selecteert u een andere resource groep en selecteert u vervolgens de oorspronkelijke resource groep.

8. Voer de volgende URL in voor de *_artifactsLocation*: `https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/`
9. Voer het nieuwe totale aantal sessie-hosts in dat u wilt in het *Rdsh-aantal exemplaren*. Als u uw hostgroep bijvoorbeeld uitbreidt van vijf sessie-hosts tot acht, voert u **8** in.
10. Voer hetzelfde bestaande domein wachtwoord in dat u hebt gebruikt voor de bestaande domein-UPN. Wijzig de gebruikers naam niet, omdat dat resulteert in een fout wanneer u de sjabloon uitvoert.
11. Voer hetzelfde Tenant beheerders wachtwoord in dat u hebt gebruikt voor de gebruiker of de toepassings-ID die u hebt opgegeven voor de UPN-naam *of toepassings-id van de Tenant beheerder*. Wijzig de gebruikers naam opnieuw.
12. Voltooi de inzending om uw hostgroep uit te breiden.

## <a name="run-the-azure-marketplace-offering"></a>De Azure Marketplace-aanbieding uitvoeren

Volg de instructies in [een hostgroep maken met behulp van de Azure Marketplace](create-host-pools-azure-marketplace-2019.md) totdat u [de Azure Marketplace-aanbieding hebt uitgevoerd om een nieuwe hostgroep in te richten](create-host-pools-azure-marketplace-2019.md#run-the-azure-marketplace-offering-to-provision-a-new-host-pool). Wanneer u dat punt krijgt, moet u de volgende informatie invoeren voor elk tabblad:

### <a name="basics"></a>Basisbeginselen

Alle waarden in deze sectie moeten overeenkomen met wat u hebt opgegeven tijdens de eerste keer dat u de hostgroep en de Vm's voor de host maakt, met uitzonde ring van *standaard bureaublad gebruikers*:

1.    Selecteer bij *abonnement* het abonnement waarin u de hostgroep voor het eerst hebt gemaakt.
2.    Voor *resource groep* selecteert u dezelfde resource groep waar de virtuele machines van de hostgroep-host zich bevinden.
3.    Selecteer voor *regio* de regio waarin de bestaande vm's van de hostgroep-host zich bevinden.
4.    Voer bij *naam van Hostpool* de naam van de bestaande hostgroep in.
5.    Selecteer bij *bureau blad* het type bureau blad dat overeenkomt met de bestaande hostgroep.
6.    Voor *standaard bureaublad gebruikers* voert u een door komma's gescheiden lijst in van alle extra gebruikers die u wilt aanmelden bij de virtuele Windows-desktop clients en toegang tot een bureau blad nadat de Azure Marketplace-aanbieding is voltooid. Als u bijvoorbeeld wilt toewijzen user3@contoso.com en toegang wilt krijgen, voert u in user4@contoso.com user3@contoso.com user4@contoso.com .
7.    Selecteer **volgende: virtuele machine configureren**.

>[!NOTE]
>Behalve voor *standaard bureaublad gebruikers* moeten alle velden overeenkomen met precies wat is geconfigureerd in de bestaande hostgroep. Als er een niet-overeenkomend resultaat is, resulteert dit in een nieuwe hostgroep.

### <a name="configure-virtual-machines"></a>Virtuele machines configureren

Alle parameter waarden in deze sectie moeten overeenkomen met wat u hebt opgegeven tijdens de eerste keer dat u de hostgroep en de Vm's voor de host maakt, met uitzonde ring van het totale aantal virtuele machines. Het aantal Vm's dat u invoert, is het aantal Vm's in de uitgebreide hostgroep:

1. Selecteer de VM-grootte die overeenkomt met de bestaande Vm's van de host.

    >[!NOTE]
    >Als de specifieke VM-grootte die u zoekt niet wordt weer gegeven in de VM-grootte kiezer, dat is omdat we deze nog niet hebben uitgevoerd op het Azure Marketplace-hulp programma.

2. Pas het *gebruiks profiel*, de *totale gebruikers* en het *aantal virtuele-machine* parameters aan om het totale aantal sessie-hosts te selecteren dat u wilt gebruiken in uw hostgroep. Als u uw hostgroep bijvoorbeeld uitbreidt van vijf sessie-hosts tot acht, configureert u deze opties voor het ophalen van 8 virtuele machines.
3. Voer een voorvoegsel in voor de namen van de virtuele machines. Als u bijvoorbeeld de naam "voor voegsel" opgeeft, worden de virtuele machines "voor voegsel-0," "voor voegsel-1," genoemd.
4. Selecteer **volgende: instellingen van de virtuele machine**.

### <a name="virtual-machine-settings"></a>Instellingen voor virtuele machines

Alle parameter waarden in deze sectie moeten overeenkomen met wat u hebt opgegeven tijdens de eerste keer dat u de hostgroep en de Vm's van de host maakt:

1. Voer voor *installatie kopie bron* en *installatie kopie versie van het besturings systeem* dezelfde informatie in die u hebt opgegeven toen u de hostgroep voor het eerst maakte.
2. Voer voor *AD-domein deelname aan UPN* en de bijbehorende wacht woorden dezelfde informatie in die u hebt opgegeven toen u de hostgroep voor het eerst maakte om de vm's toe te voegen aan het Active Directory domein. Deze referenties worden gebruikt voor het maken van een lokaal account op uw virtuele machines. U kunt deze lokale accounts later opnieuw instellen om hun referenties te wijzigen.
3. Selecteer voor de gegevens van het virtuele netwerk hetzelfde virtuele netwerk en subnet voor waar uw bestaande hostgroep-sessiehost Vm's zich bevinden.
4. Selecteer **volgende: informatie over virtueel Windows-bureau blad configureren**.

### <a name="windows-virtual-desktop-information"></a>Windows-informatie over virtueel bureau blad

Alle parameter waarden in deze sectie moeten overeenkomen met wat u hebt opgegeven tijdens de eerste keer dat u de hostgroep en de Vm's van de host maakt:

1. Voer voor *Naam van Windows Virtual Desktop-tenantgroep* de naam in van de tenantgroep die uw tenant bevat. Laat hier de standaardwaarde ongewijzigd, tenzij u een specifieke tenantgroepsnaam hebt ontvangen.
2. Voer voor *Naam van Windows Virtual Desktop-tenant* de naam in van de tenant waarin u deze hostgroep gaat maken.
3. Geef dezelfde referenties op die u hebt gebruikt toen u de hostgroep en de Vm's voor de host hebt gemaakt. Als u een Service-Principal gebruikt, geeft u de ID op van het Azure Active Directory exemplaar waarin uw Service-Principal zich bevindt.
4. Selecteer **volgende: controleren + maken**.

## <a name="run-the-github-azure-resource-manager-template"></a>De GitHub-sjabloon voor de Azure Resource Manager uitvoeren

Volg de instructies in [Run the Azure Resource Manager-sjabloon voor het inrichten van een nieuwe hostgroep](create-host-pools-arm-template.md#run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool) en geef alle dezelfde parameter waarden op, met uitzonde ring van het *aantal exemplaren van de Rdsh*. Voer het aantal Vm's voor de sessie-host in dat u wilt in de hostgroep nadat u de sjabloon hebt uitgevoerd. Als u uw hostgroep bijvoorbeeld uitbreidt van vijf sessie-hosts tot acht, voert u **8** in.

## <a name="next-steps"></a>Volgende stappen

Nu u uw bestaande hostgroep hebt uitgebreid, kunt u zich aanmelden bij een virtueel-bureaubladclient van Windows om ze te testen als onderdeel van een gebruikers sessie. U kunt verbinding maken met een sessie met een van de volgende clients:

- [Verbinding maken met de Windows Desktop-client](connect-windows-7-10-2019.md)
- [Verbinding maken met de webclient](connect-web-2019.md)
- [Verbinding maken met de Android-client](connect-android-2019.md)
- [Verbinding maken met de macOS-client](connect-macos-2019.md)
- [Verbinding maken met de iOS-client](connect-ios-2019.md)
