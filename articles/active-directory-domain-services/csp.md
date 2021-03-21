---
title: Azure AD Domain Services voor Cloud solution providers | Microsoft Docs
description: Meer informatie over het inschakelen en beheren van Azure Active Directory Domain Services beheerde domeinen voor Azure Cloud solution providers
services: active-directory-ds
author: justinha
ms.assetid: 56ccb219-11b2-4e43-9f07-5a76e3cd8da8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: d8edafff9b6534e5f1ce1c4581595ee187dfd432
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96619896"
---
# <a name="azure-active-directory-domain-services-deployment-and-management-for-azure-cloud-solution-providers"></a>Implementatie en beheer van Azure Cloud Solution Providers Azure Active Directory Domain Services

Azure Cloud Solution Providers (CSP) is een programma voor micro soft-partners en biedt een licentie kanaal voor verschillende micro soft-Cloud Services. Met Azure CSP kunnen partners verkopen beheren, eigenaar worden van de facturerings relatie, technische en facturerings ondersteuning bieden en het individuele contact punt van de klant zijn. Daarnaast biedt Azure CSP een volledige set hulpprogram ma's, waaronder een Self-Service Portal en bijbehorende Api's. Met deze hulpprogram ma's kunnen CSP-partners eenvoudig Azure-resources inrichten en beheren, en factuur maken voor klanten en hun abonnementen.

De [Portal van het partner centrum](/partner-center/azure-plan-lp) is het toegangs punt voor alle Azure CSP-partners en biedt uitgebreide mogelijkheden voor klant beheer, geautomatiseerde verwerking en meer. Azure CSP-partners kunnen partner centrum mogelijkheden gebruiken met behulp van een webinterface of met behulp van Power shell en verschillende API-aanroepen.

In het volgende diagram ziet u hoe het CSP-model op een hoog niveau werkt. Hier heeft Contoso een Azure Active Directory-Tenant (Azure AD). Ze hebben een samen werking met een CSP, die resources implementeert en beheert in hun Azure CSP-abonnement. Contoso kan ook normale (direct) Azure-abonnementen hebben, die rechtstreeks aan contoso worden gefactureerd.

![Overzicht van het CSP-model](./media/csp/csp_model_overview.png)

De Tenant van de CSP-partner heeft drie speciale agent groepen: *beheerders* agenten, *helpdesk* agenten en *verkoop* medewerkers.

De groep *Administrator* -agents wordt toegewezen aan de rol Tenant beheerder in de Azure AD-Tenant van contoso. Als gevolg hiervan heeft een gebruiker die deel uitmaakt van de groep Administrator-agents van de CSP-partner, beheerders bevoegdheden voor tenants in de Azure AD-Tenant van contoso.

Wanneer de CSP-partner een Azure CSP-abonnement voor contoso heeft ingericht, wordt de groep Administrator-agents toegewezen aan de rol van eigenaar voor dat abonnement. Als gevolg hiervan beschikken de beheerders agents van de CSP-partner over de vereiste bevoegdheden om Azure-resources zoals virtuele machines, virtuele netwerken en Azure AD Domain Services namens Contoso in te richten.

Zie [Azure CSP Overview](/partner-center/azure-plan-lp) (Engelstalig) voor meer informatie.

## <a name="benefits-of-using-azure-ad-ds-in-an-azure-csp-subscription"></a>Voor delen van het gebruik van Azure AD DS in een Azure CSP-abonnement

Azure Active Directory Domain Services (Azure AD DS) biedt beheerde domein Services, zoals domein deelname, groeps beleid, LDAP, Kerberos/NTLM-verificatie die volledig compatibel is met Windows Server Active Directory Domain Services. Over de tien tallen zijn veel toepassingen gebouwd om te werken met AD met behulp van deze mogelijkheden. Veel Independent Software Vendors (Isv's) hebben gebouwde en geïmplementeerde toepassingen op locatie van hun klanten. Deze toepassingen zijn moeilijk te ondersteunen, omdat u vaak toegang nodig hebt tot de verschillende omgevingen waarin de toepassingen worden geïmplementeerd. Met Azure CSP-abonnementen hebt u een eenvoudiger alternatief met de schaal en flexibiliteit van Azure.

Azure AD DS ondersteunt Azure CSP-abonnementen. U kunt uw toepassing implementeren in een Azure CSP-abonnement dat is gekoppeld aan de Azure AD-Tenant van uw klant. Als gevolg hiervan kunnen uw werk nemers (ondersteunings medewerkers) de virtuele machines beheren, beheren en onderhouden waarop uw toepassing wordt geïmplementeerd met de bedrijfs referenties van uw organisatie.

U kunt ook een door Azure AD DS beheerd domein implementeren in de Azure AD-Tenant van uw klant. Uw toepassing is vervolgens verbonden met het beheerde domein van uw klant. Mogelijkheden in uw toepassing die gebruikmaken van Kerberos/NTLM, LDAP of de [System. Directory Services-API](/dotnet/api/system.directoryservices) werken naadloos op het domein van uw klant. Eind gebruikers profiteren van het gebruik van uw toepassing als een service, zonder dat u zich zorgen hoeft te maken over het onderhouden van de infra structuur waarop de toepassing wordt geïmplementeerd.

Alle kosten voor Azure-resources die u in het abonnement gebruikt, inclusief Azure AD DS, worden terugbetaald. U behoudt de volledige controle over de relatie met de klant wanneer deze van toepassing is op verkoop, facturering, technische ondersteuning enz. Met de flexibiliteit van het Azure CSP-platform kan een klein team van ondersteunings agenten veel dergelijke klanten onderhouden die exemplaren van uw toepassing hebben geïmplementeerd.

## <a name="csp-deployment-models-for-azure-ad-ds"></a>CSP-implementatie modellen voor Azure AD DS

Er zijn twee manieren waarop u Azure AD DS kunt gebruiken met een Azure CSP-abonnement. Kies het juiste abonnement op basis van de beveiligings-en eenvoud overwegingen die uw klanten hebben.

### <a name="direct-deployment-model"></a>Model voor directe implementatie

In dit implementatie model wordt Azure AD DS ingeschakeld binnen een virtueel netwerk dat deel uitmaakt van het abonnement van de Azure CSP. De beheerders agents van de CSP-partner hebben de volgende bevoegdheden:

* *Globale beheerders* bevoegdheden in de Azure AD-Tenant van de klant.
* *Eigenaar* van het abonnement op het abonnement van de Azure CSP.

![Model voor directe implementatie](./media/csp/csp_direct_deployment_model.png)

In dit implementatie model kunnen beheerders agents van de CSP-provider identiteiten voor de klant beheren. Deze beheerders agents kunnen taken uitvoeren zoals het inrichten van nieuwe gebruikers of groepen, of het toevoegen van toepassingen binnen de Azure AD-Tenant van de klant.

Dit implementatie model kan geschikt zijn voor kleinere organisaties die geen specifieke identiteits beheerder hebben of liever voor de CSP-partner om namens hen identiteiten te beheren.

### <a name="peered-deployment-model"></a>Model voor gepeerd implementatie

In dit implementatie model wordt Azure AD DS ingeschakeld binnen een virtueel netwerk dat deel uitmaakt van de klant, een direct Azure-abonnement dat door de klant is betaald. De CSP-partner kan toepassingen implementeren in een virtueel netwerk dat deel uitmaakt van het CSP-abonnement van de klant. De virtuele netwerken kunnen vervolgens worden verbonden met Azure Virtual Network-peering.

Met deze implementatie kunnen werk belastingen of toepassingen die worden geïmplementeerd door de CSP-partner in het abonnement van de Azure CSP, verbinding maken met het beheerde domein van de klant dat is ingericht in het directe Azure-abonnement van de klant.

![Model voor gepeerd implementatie](./media/csp/csp_peered_deployment_model.png)

Dit implementatie model biedt een schei ding van bevoegdheden en stelt de helpdesk medewerkers van de CSP-partner in staat om het Azure-abonnement te beheren en resources binnen het te implementeren en te beheren. De Help Desk-agents van de CSP-partner hoeven echter geen globale beheerders bevoegdheden te hebben voor de Azure AD-Directory van de klant. De identiteits beheerders van de klant kunnen de identiteiten voor hun organisatie blijven beheren.

Dit implementatie model is mogelijk geschikt voor scenario's waarbij een ISV een gehoste versie van hun on-premises toepassing biedt, die ook verbinding moet maken met de Azure AD van de klant.

## <a name="administer-azure-ad-ds-in-csp-subscriptions"></a>Azure-AD DS in CSP-abonnementen beheren

De volgende belang rijke overwegingen zijn van toepassing wanneer u een beheerd domein beheert in een Azure CSP-abonnement:

* **CSP-beheerders kunnen een beheerd domein inrichten met hun referenties:** Azure AD DS ondersteunt Azure CSP-abonnementen. Gebruikers die deel uitmaken van de groep Administrator-agents van een CSP-partner kunnen een nieuw beheerd domein inrichten.

* **Met csp's kunnen scripts worden gemaakt van nieuwe beheerde domeinen voor hun klanten met behulp van Power shell:** Zie [Azure AD DS inschakelen met behulp van Power shell](powershell-create-instance.md) voor meer informatie.

* **CSP-Administrator-agents kunnen geen voortdurende beheer taken uitvoeren op het beheerde domein met behulp van hun referenties:** Gebruikers van CSP-beheerder kunnen geen routine beheer taken uitvoeren binnen het beheerde domein met behulp van hun referenties. Deze gebruikers zijn extern voor de Azure AD-Tenant van de klant en hun referenties zijn niet beschikbaar in de Azure AD-Tenant van de klant. Azure AD DS heeft geen toegang tot de Kerberos-en NTLM-wachtwoord-hashes voor deze gebruikers, zodat gebruikers niet kunnen worden geverifieerd in beheerde domeinen.

  > [!WARNING]
  > U moet een gebruikers account in de directory van de klant maken om lopende beheer taken uit te voeren op het beheerde domein.
  >
  > U kunt zich niet aanmelden bij het beheerde domein met behulp van de referenties van de CSP-beheer gebruiker. Gebruik de referenties van een gebruikers account dat deel uitmaakt van de Azure AD-Tenant van de klant. U hebt deze referenties nodig voor taken zoals het samen voegen van Vm's aan het beheerde domein, het beheren van DNS of het beheren van groepsbeleid.

* **Het gebruikers account dat is gemaakt voor doorlopend beheer moet worden toegevoegd aan de groep *Aad DC-Administrators* :** de groep *Aad DC-Administrators* heeft bevoegdheden om bepaalde gedelegeerde beheer taken uit te voeren op het beheerde domein. Deze taken omvatten het configureren van DNS, het maken van organisatie-eenheden en het beheren van groeps beleid.
    
    Voor een CSP-partner om deze taken uit te voeren op een beheerd domein, moet een gebruikers account worden gemaakt in de Azure AD-Tenant van de klant. De referenties voor dit account moeten worden gedeeld met de beheerders agents van de CSP-partner. Daarnaast moet dit gebruikers account worden toegevoegd aan de groep *Aad DC-Administrators* om configuratie taken op het beheerde domein in te scha kelen die kunnen worden uitgevoerd met dit gebruikers account.

## <a name="next-steps"></a>Volgende stappen

Als u aan de slag wilt gaan, moet u zich [Inschrijven voor het Azure CSP-programma](/partner-center/enrolling-in-the-csp-program). Vervolgens kunt u Azure AD Domain Services inschakelen met behulp van [de Azure Portal](tutorial-create-instance.md) of [Azure PowerShell](powershell-create-instance.md).