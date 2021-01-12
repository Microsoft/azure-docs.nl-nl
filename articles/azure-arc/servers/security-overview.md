---
title: Beveiligingsoverzicht
description: Beveiligings informatie over servers met Azure Arc ingeschakeld.
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: aa8653b783e7eb3e211b7514831604dd5642cfbe
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98117024"
---
# <a name="azure-arc-for-servers-security-overview"></a>Overzicht van Azure Arc for servers-beveiliging

In dit artikel worden de beveiligings configuratie en overwegingen beschreven die u moet evalueren voordat u Azure Arc-servers in uw bedrijf implementeert.

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

Elke Azure Arc-server heeft een beheerde identiteit als onderdeel van een resource groep binnen een Azure-abonnement. deze identiteit vertegenwoordigt de server waarop on-premises of andere Cloud omgevingen worden uitgevoerd. Toegang tot deze bron wordt beheerd door standaard [toegangs beheer op basis van rollen](../../role-based-access-control/overview.md). Op de pagina [**Access Control (IAM)**](../../role-based-access-control/role-assignments-portal.md) in de Azure Portal kunt u controleren wie toegang heeft tot uw Azure Arc-server.

:::image type="content" source="./media/security-overview/access-control-page.png" alt-text="Toegangs beheer voor Server voor Azure Arc ingeschakeld" border="false" lightbox="./media/security-overview/access-control-page.png":::

Gebruikers en toepassingen die [Inzender](../../role-based-access-control/built-in-roles.md#contributor) of beheerdersrol toegang tot de resource hebben verleend, kunnen wijzigingen aanbrengen in de resource, inclusief het implementeren of verwijderen van [extensies](manage-vm-extensions.md) op de computer. Uitbrei dingen kunnen wille keurige scripts bevatten die worden uitgevoerd in een geprivilegieerde context. Houd er dus rekening mee dat alle mede werkers van de Azure-resource een indirecte beheerder van de server zijn.

De functie voor **onboarding van Azure Connected machine** is beschikbaar voor onboarding en kan alleen in azure nieuwe, met Arc ingeschakelde servers lezen of maken. Het kan niet worden gebruikt voor het verwijderen van eerder geregistreerde servers of voor het beheren van uitbrei dingen. Als best practice wordt u aangeraden deze rol alleen toe te wijzen aan de Service-Principal Azure Active Directory (Azure AD) die wordt gebruikt om machines op de juiste schaal uit te voeren.

Gebruikers als lid van de rol **beheerder van Azure Connected machine resource** kunnen een machine lezen, wijzigen, onboarden en verwijderen. Deze rol is ontworpen ter ondersteuning van het beheer van servers met Arc-functionaliteit, maar niet voor andere resources in de resource groep of het abonnement.

## <a name="agent-security-and-permissions"></a>Agent beveiliging en machtigingen

Als u de Azure Connected machine agent (azcmagent) wilt beheren op Windows, moet uw gebruikers account lid zijn van de lokale groep Administrators. Voor Linux hebt u machtigingen voor basis toegang nodig.

De Azure Connected machine-agent bestaat uit drie services die op uw computer worden uitgevoerd.

* De Hybrid Instance Metadata Service-Service (himds) is verantwoordelijk voor alle kern functionaliteit van Arc. Dit omvat het verzenden van heartbeats naar Azure, het weer geven van een meta gegevens service van een lokaal exemplaar voor andere apps om meer te weten te komen over de Azure-Resource-ID van de machine en Azure AD-tokens op te halen om te verifiëren bij andere Azure-Services. Deze service wordt uitgevoerd als een niet-gemachtigd virtueel service account in Windows en als de **himds** -gebruiker op Linux.

* De gast configuratie service (GCService) is verantwoordelijk voor het evalueren van Azure Policy op de computer.

* De ExtensionService (gast Configuration extension service) is verantwoordelijk voor het installeren, bijwerken en verwijderen van extensies (agents, scripts of andere software) op de computer.

De gast configuratie-en uitbreidings services worden uitgevoerd als lokaal systeem op Windows en als root op Linux.

## <a name="using-a-managed-identity-with-arc-enabled-servers"></a>Een beheerde identiteit gebruiken met servers waarop Arc is ingeschakeld

Standaard kan de door de Arc gehoste identiteit van het Azure Active Directory systeem alleen worden gebruikt om de status van de met Arc ingeschakelde server in azure bij te werken. Bijvoorbeeld de *laatst waargenomen* heartbeat-status. U kunt eventueel extra rollen toewijzen aan de identiteit als een toepassing op uw server de door het systeem toegewezen identiteit gebruikt voor toegang tot andere Azure-Services.

Hoewel hybride Instance Metadata Service toegankelijk is voor elke toepassing die op de computer wordt uitgevoerd, kunnen alleen geautoriseerde toepassingen een Azure AD-token aanvragen voor de aan het systeem toegewezen identiteit. Bij de eerste poging om de token-URI te openen, genereert de service een wille keurig gegenereerde cryptografische Blob op een locatie op het bestands systeem die alleen door vertrouwde bellers kan worden gelezen. De aanroeper moet vervolgens het bestand lezen (bewijs dat het over de juiste machtigingen beschikt) en de aanvraag opnieuw proberen met de bestands inhoud in de autorisatie-header om een Azure AD-token op te halen.

* In Windows moet de aanroeper lid zijn van de lokale groep **Administrators** of de groep **Hybrid agent extension Applications** om de BLOB te lezen.

* In Linux moet de aanroeper lid zijn van de groep **himds** om de BLOB te lezen.

## <a name="using-disk-encryption"></a>Schijf versleuteling gebruiken

De Azure Connected machine-agent gebruikt verificatie van de open bare sleutel om te communiceren met de Azure-service. Nadat u een server aan Azure Arc hebt toegevoegd, wordt een persoonlijke sleutel op de schijf opgeslagen en gebruikt wanneer de agent communiceert met Azure. Bij dief stal kan de persoonlijke sleutel worden gebruikt op een andere server om te communiceren met de service en te reageren alsof de oorspronkelijke server. Dit omvat het verkrijgen van toegang tot de door het systeem toegewezen identiteit en alle bronnen waartoe de identiteit toegang heeft. Het bestand met de persoonlijke sleutel wordt beveiligd zodat het **himds** -account de toegang niet kan lezen. Om offline aanvallen te voor komen, raden we u ten zeerste aan de volledige schijf versleuteling (bijvoorbeeld BitLocker, DM-cryptografie enz.) te gebruiken op het besturingssysteem volume van uw server.

## <a name="next-steps"></a>Volgende stappen

Lees voordat u Arc-servers evalueert of inschakelt op meerdere hybride machines het artikel [Overzicht van Connected Machine Agent](agent-overview.md) om inzicht te krijgen in de vereisten, de technische details van de agent en de implementatiemethoden.
