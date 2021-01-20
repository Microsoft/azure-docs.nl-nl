---
title: De agent voor Azure AD Connect-inrichting installeren
description: Meer informatie over het installeren van de Azure AD Connect inrichtings agent en hoe u deze kunt configureren in de Azure Portal.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/16/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 742b9fc79489feba8192b6e62a6431bb37f55ad4
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/20/2021
ms.locfileid: "98613367"
---
# <a name="install-the-azure-ad-connect-provisioning-agent"></a>De agent voor Azure AD Connect-inrichting installeren
Dit document helpt u bij het installatie proces voor de Azure Active Directory (Azure AD) Connect inrichtings agent en hoe u deze in eerste instantie kunt configureren in de Azure Portal.

>[!IMPORTANT]
>In de volgende installatie-instructies wordt ervan uitgegaan dat aan alle [vereisten](how-to-prerequisites.md) is voldaan.

Het installeren en configureren van de Azure AD Connect Cloud synchronisatie wordt uitgevoerd in de volgende stappen:

- [Door groep beheerde service accounts](#group-managed-service-accounts) 
- [De agent installeren](#install-the-agent)
- [Agentinstallatie verifiëren](#verify-agent-installation)


## <a name="group-managed-service-accounts"></a>Door groep beheerde serviceaccounts
Een beheerd service account voor een groep is een beheerd domein account dat automatische wachtwoord beheer, vereenvoudigd Service Principal Name (SPN)-beheer biedt, de mogelijkheid om het beheer te delegeren aan andere beheerders en deze functionaliteit uit te breiden op meerdere servers.  Azure AD Connect Cloud Sync ondersteunt en beveelt het gebruik aan van een door een groep beheerd service account voor het uitvoeren van de agent.  Zie [beheerde service accounts voor groepen](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview) voor meer informatie over een gMSA 


### <a name="upgrading-an-existing-agent-to-use-the-gmsa-account"></a>Een bestaande agent bijwerken voor het gebruik van het gMSA-account
Als u een bestaande agent wilt bijwerken voor gebruik van het gMSA-account dat tijdens de installatie is gemaakt, moet u de Agent service gewoon bijwerken naar de nieuwste versie door de AADConnectProvisioningAgent.msi uit te voeren.  Hiermee wordt de service bijgewerkt naar de meest recente versie.  Voer de installatie wizard nu opnieuw uit en geef de referenties op om het account te maken wanneer u hierom wordt gevraagd.



## <a name="install-the-agent"></a>De agent installeren
Voer de volgende stappen uit om de agent te installeren.

 1. Meld u aan bij de server die u wilt gebruiken met beheerders machtigingen voor de onderneming.
 2. Meld u aan bij de Azure Portal en ga vervolgens naar **Azure Active Directory**.
 3. Selecteer in het linkermenu **Azure AD Connect**.
 4. Selecteer **Cloud Sync beheren**  >  **Alle agents controleren**.
 5. Down load de Azure AD Connect-inrichtings agent vanuit de Azure Portal.
   ![On-premises agent downloaden](media/how-to-install/install-9.png)</br>
 6. Accepteer de voor waarden en klik op downloaden.
 7. Voer het installatie programma voor de Azure AD Connect-inrichting uit AADConnectProvisioningAgentSetup.msi.
 8. Accepteer de licentie voorwaarden op het scherm Microsoft Azure AD het pakket voor het maken van een **inrichtings agent** en selecteer **installeren**.
   ![Microsoft Azure AD scherm voor het inrichtings agent-pakket koppelen](media/how-to-install/install-1.png)</br>
 9. Nadat deze bewerking is voltooid, wordt de configuratie wizard gestart. Meld u aan met uw globale beheerdersreferenties voor Azure AD.
 10. Selecteer in het **scherm Service account configureren** de optie **gMSA maken** of **aangepaste gMSA gebruiken**.  Als u de agent toestaat het account te maken, krijgt deze de naam provAgentgMSA $. Als u **aangepaste GMSA gebruiken** opgeeft, wordt u gevraagd dit account op te geven.
 11. Voer de referenties voor de domein beheerder in om het door de groep beheerde service account te maken dat wordt gebruikt om de Agent service uit te voeren. Klik op **Volgende**.  
   ![GMSA maken](media/how-to-install/install-12.png)</br>
 12. Selecteer in het scherm **verbinding maken Active Directory** de optie **map toevoegen**. Meld u vervolgens aan met uw Active Directory-beheerders account. Met deze bewerking wordt uw on-premises Directory toegevoegd. 
 13. Desgewenst kunt u de voor keur van domein controllers die door de agent worden gebruikt, beheren door de **domein controller selecteren prioriteit** te selecteren en de lijst met domein controllers te ordenen.   Klik op **OK**.
  ![Domein controllers best Ellen](media/how-to-install/install-2a.png)</br>
 14. Selecteer **Next**.
  ![Active Directory scherm verbinden](media/how-to-install/install-3a.png)</br>
 15.  Controleer op het scherm installatie van de **agent** de instellingen en het account dat wordt gemaakt en klik op **bevestigen**.
  ![Instellingen bevestigen](media/how-to-install/install-11.png)</br>
 16. Nadat deze bewerking is voltooid, ziet u dat **de installatie van de agent is voltooid.** Selecteer **Afsluiten**.
  ![Scherm configuratie voltooid](media/how-to-install/install-4a.png)</br>
 17. Als u nog steeds het scherm eerste **Microsoft Azure AD Connect inrichtings agent-pakket** ziet, selecteert u **sluiten**.

## <a name="verify-agent-installation"></a>Agentinstallatie verifiëren
Verificatie van de agent vindt plaats in de Azure Portal en op de lokale server waarop de agent wordt uitgevoerd.

### <a name="azure-portal-agent-verification"></a>Verificatie van Azure Portal-agent
Voer de volgende stappen uit om te controleren of de agent wordt gezien door Azure.

 1. Meld u aan bij de Azure-portal.
 2. Selecteer aan de linkerkant **Azure Active Directory**  >  **Azure AD CONNECT**. Selecteer in het midden **Cloud synchronisatie beheren**.

   ![Azure Portal](media/how-to-install/install-6.png)</br>

 3.  Selecteer **Alle agents controleren** op het **Azure AD Connect scherm Cloud synchronisatie** .

   ![Optie Alle agents controleren](media/how-to-install/install-7.png)</br>
 
 4. Op het scherm **on-premises Provisioning agents** ziet u de agents die u hebt geïnstalleerd. Controleer of de agent in kwestie beschikbaar is en op *Uitgeschakeld* staat.

   ![Scherm on-premises ingerichte agents](media/how-to-install/verify-1.png)</br>



### <a name="on-the-local-server"></a>Op de lokale server
Voer de volgende stappen uit om te controleren of de agent wordt uitgevoerd.

1.  Meld u met een beheerders account aan bij de server.
1.  Open **Services** door te navigeren naar de service of **door te gaan** met het  >  **uitvoeren** van  >  **Services. msc**.
1.  Zorg er bij **Services** voor dat **Microsoft Azure AD connect agent updater** en **Microsoft Azure AD Connect inrichtings agent** zijn, en de status wordt *uitgevoerd*.

    ![Scherm Services](media/how-to-install/troubleshoot-1.png)

>[!IMPORTANT]
>De agent is geïnstalleerd, maar moet worden geconfigureerd en ingeschakeld voordat de synchronisatie van gebruikers wordt gestart. Zie [een nieuwe configuratie voor Azure AD Connect Cloud synchronisatie maken](how-to-configure.md)voor informatie over het configureren van een nieuwe agent.




## <a name="next-steps"></a>Volgende stappen 

- [Wat is inrichting?](what-is-provisioning.md)
- [Wat is Azure AD Connect Cloud synchronisatie?](what-is-cloud-sync.md)
- [Maak een nieuwe configuratie voor Azure AD Connect Cloud synchronisatie](how-to-configure.md).

