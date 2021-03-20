---
title: Een door Azure beheerde toepassing plannen voor een Azure-toepassings aanbieding
description: Meer informatie over wat er is vereist voor het maken van een beheerd toepassings abonnement voor een nieuwe Azure-toepassings aanbieding met behulp van de portal voor commerciële Marketplace in het micro soft Partner Center.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: 694f501efc565ed498c1c8d8e2e38326277e8605
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96621413"
---
# <a name="plan-an-azure-managed-application-for-an-azure-application-offer"></a>Een door Azure beheerde toepassing plannen voor een Azure-toepassings aanbieding

Een Azure _Managed Application_ -abonnement is een manier om een Azure-toepassings aanbieding te publiceren in azure Marketplace. Als u dit nog niet hebt gedaan, kunt u [een Azure-toepassing aanbieding plannen voor de commerciële Marketplace](plan-azure-application-offer.md).

Beheerde toepassingen zijn Transact-aanbiedingen die worden geïmplementeerd en gefactureerd via Azure Marketplace. De optie voor de vermelding die een gebruiker ziet, is nu downloaden.

Gebruik een Azure-toepassing: beheerd toepassings plan wanneer aan de volgende voor waarden is voldaan:

- U implementeert een oplossing op basis van een abonnement voor uw klant met behulp van een virtuele machine (VM) of een volledige IaaS-oplossing (infra structuur als een service).
- U of uw klant vereist dat de oplossing wordt beheerd door een partner. Een partner kan bijvoorbeeld een systeem integrator of een managed service provider (MSP) zijn.

## <a name="managed-application-offer-requirements"></a>Vereisten voor de aanbieding van beheerde toepassingen

| Vereisten | Details |
| ------------ | ------------- |
| Een Azure-abonnement | Beheerde toepassingen moeten worden geïmplementeerd op het abonnement van een klant, maar kunnen worden beheerd door derden. |
| Facturering en meting | De resources zijn opgenomen in het Azure-abonnement van een klant. Vm's die gebruikmaken van het betalen per gebruik-betalings model, worden door micro soft gefactureerd met de klant via het Azure-abonnement van de klant. <br><br> Voor uw Vm's met uw eigen licentie, factureert micro soft kosten voor de infra structuur die in het abonnement van de klant worden gemaakt, maar u kunt ook de kosten voor software licenties ook rechtstreeks aan de klant door geven. |
| Azure-compatibele virtuele harde schijf (VHD) | Vm's moeten zijn gebouwd op Windows of Linux. Zie voor meer informatie:<br> • [Een Azure VM-technische Asset maken](./azure-vm-create-certification-faq.md#address-a-vulnerability-or-an-exploit-in-a-vm-offer) (voor Windows-vhd's).<br> •  [Linux-distributies die zijn goedgekeurd op Azure](../virtual-machines/linux/endorsed-distros.md) (voor Linux-vhd's). |
| Gebruikskenmerken van klanten | Alle nieuwe aanbiedingen van Azure Application moeten ook een GUID voor [klant gebruik van Azure-partners](azure-partner-customer-usage-attribution.md) bevatten. Zie voor meer informatie over het gebruik van de klant en hoe u deze functie inschakelt, de [klant gebruiks toewijzing van Azure-partners](azure-partner-customer-usage-attribution.md). |
| Implementatie pakket | U hebt een implementatie pakket nodig waarmee klanten uw abonnement kunnen implementeren. Als u meerdere plannen maakt waarvoor dezelfde technische configuratie nodig is, kunt u hetzelfde pakket gebruiken. Zie de volgende sectie: implementatie pakket voor meer informatie. |
|||

> [!NOTE]
> Beheerde toepassingen moeten worden geïmplementeerd via Azure Marketplace. Als de communicatie van de klant een probleem is, kunt u contact met geïnteresseerde klanten bereiken nadat u het delen van leads hebt ingeschakeld.

## <a name="deployment-package"></a>Implementatie pakket

Het implementatie pakket bevat alle sjabloon bestanden die nodig zijn voor dit plan en eventuele extra resources, verpakt als zip-bestand.

Alle Azure-toepassingen moeten deze twee bestanden in de hoofdmap van een zip-archief bevatten:

- Een resource manager-sjabloon bestand [ met de naammainTemplate.jsop](../azure-resource-manager/managed-applications/publish-service-catalog-app.md?tabs=azure-powershell#create-the-arm-template). Met deze sjabloon worden de resources gedefinieerd die moeten worden geïmplementeerd in het Azure-abonnement van de klant. Voor voor beelden van Resource Manager-sjablonen raadpleegt u de [Azure Quick Start-sjablonen galerie](https://azure.microsoft.com/documentation/templates/) of de bijbehorende [github: Azure Resource Manager Quick](https://github.com/azure/azure-quickstart-templates) start-sjablonen opslag plaats.
- Een definitie van de gebruikers interface voor de Azure-toepassing voor het maken van toepassingen met de naam [createUiDefinition.jsop](../azure-resource-manager/managed-applications/create-uidefinition-overview.md). In de gebruikersinterface geeft u elementen op waarmee consumenten parameterwaarden kunnen opgeven.

De maximale bestands grootte die wordt ondersteund zijn:

- Tot 1 GB in totaal gecomprimeerd. zip-archief grootte
- Maxi maal 1 GB voor elk afzonderlijk niet-gecomprimeerd bestand in het zip-archief

Alle nieuwe aanbiedingen van Azure Application moeten ook een GUID voor [klant gebruik van Azure-partners](azure-partner-customer-usage-attribution.md) bevatten.

## <a name="azure-regions"></a>Azure-regio's

U kunt uw abonnement publiceren naar de open bare Azure-regio, de Azure Government regio of beide. Voordat u naar [Azure Government](../azure-government/documentation-government-manage-marketplace-partners.md)publiceert, moet u het abonnement testen en valideren in de omgeving, omdat bepaalde eind punten kunnen verschillen. Als u uw abonnement wilt instellen en testen, vraagt u een proef account aan bij [Microsoft Azure Government proef versie](https://azure.microsoft.com/global-infrastructure/government/request/).

Als uitgever bent u verantwoordelijk voor nalevings controles, beveiligings maatregelen en aanbevolen procedures. Azure Government maakt gebruik van fysiek geïsoleerde data centers en netwerken (alleen in de Verenigde Staten).

Zie [geografische Beschik baarheid en valuta ondersteuning](marketplace-geo-availability-currencies.md)voor een lijst met landen en regio's die door de commerciële Marketplace worden ondersteund.

Azure Government services verwerken gegevens die onderworpen zijn aan bepaalde wettelijke voor schriften en vereisten. Bijvoorbeeld FedRAMP, NIST 800,171 (DIB), ITAR, IRS 1075, DoD N4 en CJIS. Als u uw certificeringen voor deze Program ma's wilt controleren, kunt u Maxi maal 100 koppelingen opgeven. Deze kunnen links naar uw vermelding in het programma staan, of koppelingen naar beschrijvingen van uw naleving met hen op uw eigen websites. Deze koppelingen zijn alleen zichtbaar voor Azure Government klanten.

## <a name="choose-who-can-see-your-plan"></a>Kies wie uw abonnement kan zien

U kunt elk plan zodanig configureren dat het zichtbaar is voor iedereen (openbaar) of alleen voor een specifieke doel groep (privé). U kunt Maxi maal 100 plannen maken en tot 45 kunnen deze privé zijn. Mogelijk wilt u een privé-abonnement maken om verschillende prijs opties of technische configuraties aan specifieke klanten te bieden.

U verleent toegang tot een persoonlijk abonnement met behulp van Azure-abonnement-Id's met de optie om een beschrijving op te nemen van elke abonnements-ID die u toewijst. U kunt Maxi maal 10 abonnements-Id's hand matig of Maxi maal 10.000 abonnement-Id's toevoegen met behulp van een. CSV-bestand. Azure-abonnements-Id's worden weer gegeven als GUID'S en letters moeten kleine letters zijn.

Privé plannen worden niet ondersteund met Azure-abonnementen die zijn gemaakt via een wederverkoper van het Cloud Solution Provider-programma (CSP). Zie voor meer informatie [persoonlijke aanbiedingen in micro soft Commercial Marketplace](private-offers.md).

> [!NOTE]
> Als u een privé-abonnement publiceert, kunt u de zicht baarheid later wijzigen in openbaar. Wanneer u echter een openbaar abonnement publiceert, kunt u de zicht baarheid niet wijzigen in privé.

## <a name="define-pricing"></a>Prijzen definiëren

U moet de prijs per maand opgeven voor elk abonnement. Deze prijs is een aanvulling op de kosten van een Azure-infra structuur of een betalen per gebruik-software die wordt gemaakt door de resources die door deze oplossing worden geïmplementeerd.

Naast de prijs per maand kunt u ook prijzen voor het verbruik van niet-standaard eenheden instellen met behulp van [facturering via een Data limiet](partner-center-portal/azure-app-metered-billing.md). U kunt de prijs per maand instellen op nul en kosten in rekening gebracht als u wilt.

Prijzen worden ingesteld in USD (USD = Verenigde Staten dollar) worden omgezet in de lokale valuta van alle geselecteerde markten met de huidige wissel koersen wanneer deze worden opgeslagen. Maar u kunt ervoor kiezen om de klant prijzen voor elke markt in te stellen.

## <a name="just-in-time-jit-access"></a>Just-in-time-toegang

Met JIT-toegang kunt u verhoogde toegang tot de resources van een beheerde toepassing aanvragen voor het oplossen van problemen of onderhoud. U hebt altijd alleen-lezen toegang tot de resources, maar gedurende een bepaalde periode kunt u meer toegang hebben. Zie [just-in-time-toegang inschakelen en aanvragen voor Azure Managed Applications](../azure-resource-manager/managed-applications/request-just-in-time-access.md)voor meer informatie.

> [!NOTE]
> Zorg ervoor dat u het bestand bijwerkt `createUiDefinition.json` om deze functie te kunnen ondersteunen.

## <a name="deployment-mode"></a>Implementatiemodus

U kunt een beheerd toepassings plan configureren om de **volledige** of **incrementele** implementatie modus te gebruiken. In de volledige modus leidt een herimplementatie van de toepassing door de klant tot het verwijderen van resources in de beheerde resource groep als de resources niet zijn gedefinieerd in de [mainTemplate.jsop](../azure-resource-manager/managed-applications/publish-service-catalog-app.md?tabs=azure-powershell#create-the-arm-template). In de incrementele modus blijven bestaande resources ongewijzigd wanneer de toepassing opnieuw wordt geïmplementeerd. Zie [Azure Resource Manager implementatie modi](../azure-resource-manager/templates/deployment-modes.md)voor meer informatie.

## <a name="notification-endpoint-url"></a>URL van meldings eindpunt

U kunt optioneel een HTTPS-webhook-eind punt opgeven voor het ontvangen van meldingen over alle ruwe bewerkingen op beheerde toepassings exemplaren van een abonnement.

## <a name="customize-allowed-customer-actions-optional"></a>Toegestane klant acties aanpassen (optioneel)

U kunt optioneel opgeven welke acties klanten kunnen uitvoeren op de beheerde resources naast de `*/read` acties die standaard beschikbaar zijn.

Als u deze optie kiest, moet u de acties voor het besturings element of de toegestane gegevens opgeven, of beide. Zie voor meer informatie [over het weigeren van toewijzingen voor Azure-resources](../role-based-access-control/deny-assignments.md). Zie [Bewerkingen voor de resourceprovider van Azure Resource Manager](../role-based-access-control/resource-provider-operations.md) voor beschikbare acties. Als u bijvoorbeeld wilt toestaan dat consumenten virtuele machines opnieuw opstarten, moet u `Microsoft.Compute/virtualMachines/restart/action` toevoegen aan de toegestane acties.

## <a name="choose-who-can-manage-the-application"></a>Kies wie de toepassing kan beheren

U moet aangeven wie een beheerde toepassing kan beheren in elk van de geselecteerde Clouds: _open bare Azure_ en _Azure Government Cloud_. Verzamel de volgende gegevens:

- **Azure Active Directory TENANT-** id: de id van de Azure AD-Tenant (ook wel bekend als Directory-id) die de identiteiten bevat van de gebruikers, groepen of toepassingen waaraan u machtigingen wilt verlenen. U kunt uw Azure AD-Tenant-ID vinden op de Azure Portal, in [Eigenschappen voor Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties).
- **Autorisaties** : voeg de Azure Active Directory object-id toe van elke gebruiker, groep of toepassing waaraan u machtigingen wilt verlenen voor de beheerde resource groep. Identificeer de gebruiker op basis van de principal-ID, die u kunt vinden op de [blade Azure Active Directory gebruikers op de Azure Portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers).

Voor elke Principal-ID koppelt u een van de ingebouwde rollen van Azure AD (eigenaar of bijdrager). Met de rol die u selecteert, worden de machtigingen beschreven die de principal heeft op de resources in het abonnement van de klant. Zie [Ingebouwde rollen in Azure](../role-based-access-control/built-in-roles.md) voor meer informatie. Zie [aan de slag met RBAC in de Azure Portal](../role-based-access-control/overview.md)voor meer informatie over op rollen gebaseerd toegangs beheer (RBAC).

> [!NOTE]
> Hoewel u Maxi maal 100 autorisaties per Azure-regio kunt toevoegen, is het over het algemeen eenvoudiger om een Active Directory gebruikers groep te maken en de ID op te geven in de principal-ID. Zo kunt u meer gebruikers toevoegen aan de beheer groep nadat het plan is geïmplementeerd en de nood zaak voor het bijwerken van het plan beperken om meer autorisaties toe te voegen.

## <a name="policy-settings"></a>Beleidsinstellingen

U kunt [Azure-beleid](../governance/policy/index.yml) Toep assen op uw beheerde toepassing om nalevings vereisten voor de geïmplementeerde oplossing op te geven. Zie [Voorbeelden van Azure Policy](../governance/policy/samples/index.md) voor beleidsdefinities en de indeling van de parameterwaarden.

U kunt Maxi maal vijf beleids regels en slechts één exemplaar van elk beleids type configureren. Voor sommige beleids typen zijn aanvullende para meters vereist.

| Beleidstypen | Beleids parameters vereist |
| ------------ | ------------- |
| Versleuteling Azure SQL Database | Nee |
| Controle-instellingen voor Azure SQL Server | Ja |
| Versleuteling Azure Data Lake Store | Nee |
| Diagnostische instelling controleren | Ja |
| Naleving van resource locatie controleren | Nee |
|||

Voor elk beleids type dat u toevoegt, moet u de SKU Standard of gratis beleid koppelen. De standaard-SKU is vereist voor controle beleid. Beleids namen zijn beperkt tot 50 tekens.

## <a name="next-steps"></a>Volgende stappen

- [Een Azure-toepassings aanbieding maken in de commerciële Marketplace](create-new-azure-apps-offer.md)
