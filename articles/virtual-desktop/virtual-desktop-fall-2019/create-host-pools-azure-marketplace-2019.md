---
title: Windows Virtual Desktop (classic)-hostgroep Azure Marketplace - Azure
description: Een Windows Virtual Desktop (classic)-hostgroep maken met behulp van Azure Marketplace.
author: Heidilohr
ms.topic: how-to
ms.date: 03/31/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: d7e0862bd7519f59602f1e769301614895bd6e09
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106121877"
---
# <a name="tutorial-create-a-host-pool-in-windows-virtual-desktop-classic"></a>Zelfstudie: Een hostgroep maken in Windows Virtual Desktop (classic)

>[!IMPORTANT]
>Deze inhoud is van toepassing op Windows Virtual Desktop (classic), dat geen ondersteuning biedt voor Azure Resource Manager Windows Virtual Desktop-objecten. Raadpleeg [dit artikel](../create-host-pools-azure-marketplace.md) als u probeert Azure Resource Manager Windows Virtual Desktop-objecten te beheren.

In deze zelfstudie leert u hoe u een hostgroep maakt binnen een Windows Virtual Desktop-tenant door gebruik te maken van een Microsoft Azure Marketplace-aanbieding.

Hostgroepen zijn een verzameling van een of meer identieke virtuele machines in Windows Virtual Desktop-tenantomgevingen. Elke hostgroep kan een app-groep bevatten waarmee gebruikers kunnen communiceren, op dezelfde manier als op een fysiek bureaublad.

De taken in deze zelfstudie omvatten:

> [!div class="checklist"]
>
> * Een hostgroep maken in Windows Virtual Desktop.
> * Een resourcegroep met VM’s maken in een Azure-abonnement.
> * De VM’s koppelen aan het Active Directory-domein.
> * De VM’s registreren bij Windows Virtual Desktop.

## <a name="prerequisites"></a>Vereisten

* Een tenant in Virtual Desktop. In een eerdere [zelfstudie](tenant-setup-azure-active-directory.md) wordt een tenant gemaakt.
* [Windows Virtual Desktop PowerShell-module](/powershell/windows-virtual-desktop/overview/).

Zodra u deze module hebt, voert u de volgende cmdlet uit om u aan te melden bij uw account:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de [Azure-portal](https://portal.azure.com).

## <a name="run-the-azure-marketplace-offering-to-provision-a-new-host-pool"></a>Voer de Azure Marketplace-aanbieding uit om een nieuwe hostgroep in te richten

De Azure Marketplace-aanbieding uitvoeren om een nieuwe hostgroep in te richten:

1. Selecteer in het menu van de Azure-portal of op de **startpagina** de optie **Een resource maken**.
1. Voer **Windows Virtual Desktop** in het Marketplace-zoekvenster in.
1. Selecteer **Windows Virtual Desktop - Een hostgroep inrichten**, en selecteer vervolgens **Maken**.

Volg daarna de instructies in de volgende sectie om de gegevens voor de juiste tabbladen in te voeren.

### <a name="basics"></a>Basisbeginselen

Hier volgt wat u moet doen voor het tabblad **Basisprincipes**:

1. Selecteer een **Abonnement**.
1. Selecteer voor **Resourcegroep** de optie **Nieuwe maken**, en geef een naam op voor de nieuwe resourcegroep.
1. Selecteer een **Regio**.
1. Voer een naam in voor de hostgroep die uniek is binnen de Windows Virtual Desktop-tenant.
1. Selecteer **Type desktop**. Als u **Persoonlijk** selecteert, wordt elke gebruiker die verbinding maakt met deze hostgroep, permanent toegewezen aan een virtuele machine.
1. Voer gebruikers in die zich kunnen aanmelden bij de Windows Virtual Desktop-clients en toegang hebben tot een desktop. Gebruik een door komma's gescheiden lijst. Als u bijvoorbeeld `user1@contoso.com` en `user2@contoso.com` toegang wilt verlenen, voert u in: *`user1@contoso.com,user2@contoso.com`*
1. Selecteer voor **Locatie van metagegevensservice** dezelfde locatie als het virtuele netwerk dat is verbonden met de Active Directory-server.

   >[!IMPORTANT]
   >Als u een oplossing met alleen Azure AD DS (Azure Active Directory Domain Services) en Azure AD (Azure Active Directory) gebruikt, moet u ervoor zorgen dat u de hostgroep in dezelfde regio implementeert als uw Azure AD DS, ter voorkoming van fouten met domeindeelname en referenties.

1. Selecteer **Volgende: Virtuele machines configureren**.

### <a name="configure-virtual-machines"></a>Virtuele machines configureren

Voor het tabblad **Virtuele machines configureren**:

1. Accepteer de standaardwaarden of pas het aantal en de grootte van de virtuele machines aan.

    >[!NOTE]
    >Als de specifieke grootte van de virtuele machine die u zoekt, niet wordt weergegeven in de groottekiezer, komt dit omdat deze grootte nog niet is geïntroduceerd in het Azure Marketplace-hulpprogramma.

2. Voer een voorvoegsel in voor de namen van de virtuele machines. Voorbeeld: als u *voorvoegsel* invoert, heten de virtuele machines **voorvoegsel-0**, **voorvoegsel-1**, enzovoort.
3. Selecteer **Volgende: Instellingen voor virtuele machines**.

### <a name="virtual-machine-settings"></a>Instellingen voor virtuele machines

Voor het tabblad **Instellingen voor virtuele machines**:

1. Selecteer voor **Broninstallatiekopie** de bron en voer de juiste informatie in voor hoe deze kan worden gevonden en opgeslagen. Uw opties verschillen van Blob-opslag, Beheerde installatiekopie, en Galerie.

   Als u ervoor kiest geen beheerde schijven te gebruiken, selecteert u het opslagaccount dat het *VHD*-bestand bevat.
1. Voer de gebruikersnaam en het wachtwoord in. Dit account moet het domeinaccount zijn waarmee de virtuele machines worden gekoppeld aan het Active Directory-domein. Dezelfde gebruikersnaam en hetzelfde wachtwoord worden als lokaal account gemaakt op de virtuele machines. U kunt deze lokale accounts later opnieuw instellen.

   >[!NOTE]
   > Als u uw virtuele machines koppelt aan een Azure AD DS-omgeving, moet u ervoor zorgen dat uw domeindeelnamegebruiker lid is van de [AAD DC-beheerdersgroep](../../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group).
   >
   > Het account moet ook deel uitmaken van het beheerde Azure AD DS-domein of de Azure AD-tenant. Accounts uit externe mappen die zijn gekoppeld aan uw Azure AD-tenant, kunnen niet juist worden geverifieerd tijdens het domeindeelnameproces.

1. Selecteer het **Virtuele netwerk** dat is verbonden met de Active Directory-server, en kies vervolgens een subnet om de virtuele machines te hosten.
1. Selecteer **Volgende: Windows Virtual Desktop-gegevens**.

### <a name="windows-virtual-desktop-tenant-information"></a>Windows Virtual Desktop-tenantgegevens

Voor het tabblad **Windows Virtual Desktop-tenantgegevens**:

1. Voer voor **Naam van Windows Virtual Desktop-tenantgroep** de naam in van de tenantgroep die uw tenant bevat. Laat hier de standaardwaarde ongewijzigd, tenzij u een specifieke tenantgroepsnaam hebt ontvangen.
1. Voer voor **Naam van Windows Virtual Desktop-tenant** de naam in van de tenant waarin u deze hostgroep gaat maken.
1. Geef het referentietype op dat u wilt gebruiken voor verificatie als de RDS-eigenaar van de Windows Virtual Desktop-tenant. Voer de UPN of service-principal in, en een wachtwoord.

   Als u de [zelfstudie Service-principals en roltoewijzingen maken met PowerShell](create-service-principal-role-powershell.md) hebt voltooid, selecteert u **Service-principal**.

1. Voer voor **Service-principal**, voor **Azure AD-tenant-id**, het tenantbeheerdersaccount in voor het Azure AD-exemplaar dat de service-principal bevat. Alleen service-principals met een wachtwoordreferentie worden ondersteund.
1. Selecteer **Volgende: Controleren en maken**.

## <a name="complete-setup-and-create-the-virtual-machine"></a>Installatie voltooien en de virtuele machine maken

Controleer de installatie-informatie in **Controleren en maken**. Als u iets wilt wijzigen, gaat u terug en brengt u wijzigingen aan. Wanneer u klaar bent, selecteert u **Maken** om de hostgroep te implementeren.

Afhankelijk van het aantal virtuele machines dat u maakt, kan het 30 minuten of langer duren voordat dit proces is voltooid.

>[!IMPORTANT]
> We raden u aan om binnenkomende poort 3389 op uw virtuele machines niet te openen ter beveiliging van uw Windows Virtual Desktop-omgeving in Azure. Voor Windows Virtual Desktop is geen geopende binnenkomende poort 3389 nodig om gebruikers toegang te verlenen tot de virtuele machines van de hostgroep.
>
> Als u poort 3389 moet openen om een probleem te kunnen oplossen, raden we u aan Just-In-Time-toegang te gebruiken. Zie [Uw beheerpoorten beveiligen met Just-in-time-toegang](../../security-center/security-center-just-in-time.md) voor meer informatie.

## <a name="optional-assign-additional-users-to-the-desktop-application-group"></a>Extra gebruikers toewijzen aan de bureaubladtoepassingsgroep (optioneel)

Nadat het maken van de groep in Azure Marketplace is voltooid, kunt u meer gebruikers toewijzen aan de bureaubladtoepassingsgroep. Als u niet meer gebruikers wilt toevoegen, kunt u deze sectie overslaan.

Extra gebruikers toewijzen aan de bureaubladtoepassingsgroep:

1. Open een Powershell-venster.

1. Voer de volgende opdracht uit om u aan te melden bij de Windows Virtual Desktop-omgeving:

   ```powershell
   Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
   ```

1. Voeg gebruikers toe aan de bureaubladtoepassingsgroep met behulp van deze opdracht:

   ```powershell
   Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
   ```

   De UPN van de gebruiker moet overeenkomen met de identiteit van de gebruiker in Azure AD, bijvoorbeeld *user1@contoso.com* . Als u meerdere gebruikers wilt toevoegen, voert u de opdracht uit voor elke gebruiker.

Gebruikers die u toevoegt aan de bureaubladtoepassingsgroep, kunnen zich via een ondersteunde Extern bureaublad-client aanmelden bij Windows Virtual Desktop en een resource zien voor een sessiebureaublad.

Dit zijn de clients die momenteel worden ondersteund:

* [Extern bureaublad-client voor Windows 7 en Windows 10](connect-windows-7-10-2019.md)
* [Windows Virtual Desktop-webclient](connect-web-2019.md)

## <a name="next-steps"></a>Volgende stappen

U hebt een hostgroep gemaakt en gebruikers toegewezen voor toegang tot het bijbehorende bureaublad. U kunt de hostgroep vullen met RemoteApp-programma’s. Bekijk deze zelfstudie voor meer informatie over het beheren van apps in Windows Virtual Desktop:

> [!div class="nextstepaction"]
> [Zelfstudie: App-groepen beheren](manage-app-groups-2019.md)
