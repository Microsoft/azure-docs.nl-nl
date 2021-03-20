---
title: Azure AD Connect Health en privacy van de gebruiker | Microsoft Docs
description: In dit document wordt de privacy van de gebruiker met Azure AD Connect Health beschreven.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 04/26/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58ecdfefa79716bcc0a69063293d5a1067693a78
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89182357"
---
# <a name="user-privacy-and-azure-ad-connect-health"></a>Privacy en Azure AD Connect Health van gebruiker 

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

>[!NOTE] 
>In dit artikel worden Azure AD Connect Health en privacy van gebruikers besproken.  Zie het artikel [hier](reference-connect-user-privacy.md)voor meer informatie over Azure AD Connect en privacy van de gebruiker.

## <a name="user-privacy-classification"></a>Classificatie van gebruikers-privacy
Azure AD Connect Health valt in de **gegevens processor** categorie van de AVG-classificatie. Als een pijplijn voor het verwerken van gegevens, biedt de service voorzieningen voor gegevensverwerking aan belangrijke partners en eindgebruikers. Azure AD Connect Health genereert geen gebruikers gegevens en heeft geen onafhankelijke controle over welke persoons gegevens worden verzameld en hoe deze worden gebruikt. Het ophalen van gegevens, aggregatie, analyse en rapportage in Azure AD Connect Health zijn gebaseerd op bestaande on-premises gegevens. 

## <a name="data-retention-policy"></a>Beleid voor gegevensretentie
Azure AD Connect Health genereert geen rapporten, voert geen analyses uit of bieden meer dan dertig dagen geen inzichten. Daarom worden gegevens na 30 dagen niet opgeslagen, verwerkt of bewaard door Azure AD Connect Health. Dit ontwerp voldoet aan de AVG, het privacybeleid van Microsoft en het beleid van Azure AD inzake het bewaren van gegevens. 

Servers met actieve **Health Service-gegevens zijn niet up-to-date** - **fout** meldingen gedurende meer dan 30 opeenvolgende dagen, om aan te nemen dat geen gegevens de verbindings status hebben bereikt tijdens die periode. Deze servers worden uitgeschakeld en worden niet weer gegeven in de portal Connect Health. Als u de servers opnieuw wilt inschakelen, moet u [de Health-Agent verwijderen en opnieuw installeren](how-to-connect-health-agent-install.md). Houd er rekening mee dat dit niet geldt voor **waarschuwingen** met hetzelfde waarschuwings type. Waarschuwingen geven aan dat gedeeltelijke gegevens ontbreken op de server waarvoor u wordt gewaarschuwd. 
 
## <a name="disable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>Gegevens verzameling en-bewaking uitschakelen in Azure AD Connect Health
Met Azure AD Connect Health kunt u het verzamelen van gegevens voor elke afzonderlijke bewaakte server of voor een exemplaar van een bewaakte service stoppen. U kunt bijvoorbeeld het verzamelen van gegevens voor afzonderlijke ADFS (Active Directory Federation Services)-servers die worden bewaakt met behulp van Azure AD Connect Health stoppen. U kunt ook het verzamelen van gegevens stoppen voor het hele ADFS-exemplaar dat wordt bewaakt met behulp van Azure AD Connect Health. Wanneer u ervoor kiest om dit te doen, worden de bijbehorende servers verwijderd uit de Azure AD Connect Health Portal nadat het verzamelen van gegevens is gestopt. 

>[!IMPORTANT]
> U hebt de globale beheerders bevoegdheden van Azure AD of de rol Inzender in azure RBAC nodig om bewaakte servers van Azure AD Connect Health te verwijderen.
>
> Het verwijderen van een server-of service-exemplaar uit Azure AD Connect Health is geen omkeerbaar actie. 

### <a name="what-to-expect"></a>Wat kunt u verwachten?
Als u het verzamelen en controleren van gegevens voor een afzonderlijke bewaakte server of een exemplaar van een bewaakte service stopt, let dan op het volgende:

- Wanneer u een exemplaar van een bewaakte service verwijdert, wordt het exemplaar verwijderd uit de lijst Azure AD Connect Health Monitoring Service in de portal. 
- Wanneer u een bewaakte server of een exemplaar van een bewaakte service verwijdert, wordt de Health-Agent niet verwijderd of wordt deze van uw servers gewist. De Health-Agent is zo geconfigureerd dat er geen gegevens naar Azure AD Connect Health worden verzonden. U moet de Health-Agent hand matig verwijderen van eerder bewaakte servers.
- Als u de Health-Agent niet hebt verwijderd voordat u deze stap uitvoert, ziet u mogelijk fout gebeurtenissen op de server (s) die betrekking hebben op de Health-Agent.
- Alle gegevens die deel uitmaken van het exemplaar van de bewaakte service, worden verwijderd volgens het Microsoft Azure Bewaar beleid voor gegevens.

### <a name="disable-data-collection-and-monitoring-for-an-instance-of-a-monitored-service"></a>Gegevens verzameling en-bewaking uitschakelen voor een exemplaar van een bewaakte service
Zie [een service-exemplaar verwijderen uit Azure AD Connect Health](how-to-connect-health-operations.md#delete-a-service-instance-from-azure-ad-connect-health-service).

### <a name="disable-data-collection-and-monitoring-for-a-monitored-server"></a>Gegevens verzameling en-bewaking uitschakelen voor een bewaakte server
Zie [een server verwijderen uit Azure AD Connect Health](how-to-connect-health-operations.md#delete-a-server-from-the-azure-ad-connect-health-service).

### <a name="disable-data-collection-and-monitoring-for-all-monitored-services-in-azure-ad-connect-health"></a>Gegevens verzameling en-bewaking uitschakelen voor alle bewaakte Services in Azure AD Connect Health
Azure AD Connect Health biedt ook de mogelijkheid om het verzamelen van gegevens van **alle** geregistreerde Services in de Tenant te stoppen. We raden u aan om alle algemene beheerders een zorgvuldige overweging te nemen en te bevestigen voordat u de actie uitvoert. Zodra het proces is gestart, wordt door Connect Health Service geen gegevens van al uw services ontvangen, verwerkt en gerapporteerd. Bestaande gegevens in Connect Health-Service worden niet meer dan 30 dagen bewaard.
Als u het verzamelen van gegevens van een specifieke server wilt stoppen, volgt u de stappen bij het verwijderen van specifieke servers. Als u wilt stoppen met het verzamelen van Tenant gegevens, voert u de volgende stappen uit om het verzamelen van gegevens te stoppen en alle services van de Tenant te verwijderen.

1. Klik op **algemene instellingen** onder configuratie in de hoofd Blade. 
2. Klik op de knop **gegevens verzameling stoppen** boven aan de Blade. De andere opties voor Tenant configuratie-instellingen worden uitgeschakeld zodra het proces wordt gestart.  
 
   ![Gegevens verzameling stoppen](./media/reference-connect-health-user-privacy/gdpr4.png)
  
3. Zorg ervoor dat de lijst met onboarded Services wordt beïnvloed door het stoppen van gegevens verzamelingen. 
4. Voer de exacte Tenant naam in om de knop actie **verwijderen** in te scha kelen
5. Klik op **verwijderen** om het verwijderen van alle services te activeren. De verbindings status stopt met het ontvangen, verwerken en rapporteren van gegevens die zijn verzonden vanuit uw onboardde Services. Het hele proces van kan tot 24 uur duren. U ziet dat deze stap niet kan worden teruggedraaid. 
6. Nadat het proces is voltooid, worden er geen geregistreerde services meer weer geven in Connect Health. 

   ![Nadat de gegevens verzameling is gestopt](./media/reference-connect-health-user-privacy/gdpr5.png)

## <a name="re-enable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>Gegevens verzameling en-bewaking opnieuw inschakelen in Azure AD Connect Health
Als u de bewaking in Azure AD Connect Health opnieuw wilt inschakelen voor een eerder verwijderde bewaakte service, moet u [de Health-Agent verwijderen en opnieuw installeren](how-to-connect-health-agent-install.md) op alle servers.

### <a name="re-enable-data-collection-and-monitoring-for-all-monitored-services"></a>Gegevens verzameling en-bewaking opnieuw inschakelen voor alle bewaakte Services

Het verzamelen van Tenant gegevens kan worden hervat in Azure AD Connect Health. We raden u aan om alle algemene beheerders een zorgvuldige overweging te nemen en te bevestigen voordat u de actie uitvoert.

>[!IMPORTANT]
> De volgende stappen zijn beschikbaar na 24 uur van uitschakelen actie.
> Na het inschakelen van het verzamelen van gegevens, worden in het inzicht en de bewakings gegevens in de status van de verbinding geen verouderde gegevens weer gegeven die eerder zijn verzameld. 

1. Klik op **algemene instellingen** onder configuratie in de hoofd Blade. 
2. Klik boven aan de Blade op de knop **gegevens verzameling inschakelen** . 
 
   ![Gegevensverzameling inschakelen](./media/reference-connect-health-user-privacy/gdpr6.png)
 
3. Voer de exacte Tenant naam in om de knop **inschakelen** te activeren.
4. Klik op de knop **inschakelen** om machtigingen te verlenen voor het verzamelen van gegevens in Connect Health-Service. De wijziging wordt binnenkort toegepast. 
5. Volg het [installatie proces](how-to-connect-health-agent-install.md) om de agent opnieuw te installeren op de servers die moeten worden bewaakt en de services worden weer gegeven in de portal.  


## <a name="next-steps"></a>Volgende stappen
* [Het privacybeleid van micro soft op vertrouwens centrum bekijken](https://www.microsoft.com/trustcenter)
* [Azure AD Connect en privacy van gebruikers](reference-connect-user-privacy.md)

