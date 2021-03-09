---
title: Rollen toewijzen aan Azure Enterprise Overeenkomst service principal names
description: Dit artikel helpt u bij het toewijzen van rollen aan service-principal-namen met behulp van Power shell en REST Api's.
author: bandersmsft
ms.reviewer: ruturajd
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 03/07/2021
ms.author: banders
ms.openlocfilehash: 0f30c90bf81a837b1e78ca5f91450cf085cc91bc
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102495098"
---
# <a name="assign-roles-to-azure-enterprise-agreement-service-principal-names"></a>Rollen toewijzen aan Azure Enterprise Overeenkomst service principal names

U kunt de inschrijving van uw Enterprise Overeenkomst (EA) beheren in de [Azure Enter prise Portal](https://ea.azure.com/). U kunt verschillende rollen maken voor het beheren van uw organisatie, het weer geven van kosten en het maken van abonnementen. Dit artikel helpt u bij het automatiseren van enkele van deze taken met behulp van Azure PowerShell en REST Api's met Azure Service Principal Names (Spn's).

Voordat u begint, moet u ervoor zorgen dat u bekend bent met de volgende artikelen:

- [Enter prise Agreement-rollen](understand-ea-roles.md)
- [Aanmelden met Azure PowerShell](/powershell/azure/authenticate-azureps?view=azps-5.5.0&preserve-view=true)
- [REST-Api's aanroepen met postman](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

## <a name="create-and-authenticate-your-service-principal"></a>Uw service-principal maken en verifiëren

Als u EA-acties wilt automatiseren met behulp van een SPN, moet u een Azure Active Directory-toepassing (Azure AD) maken. Het kan op een geautomatiseerde manier worden geverifieerd. Lees de volgende artikelen en volg de onderstaande stappen om uw service-principal te maken en te verifiëren.

1. [Een service-principal maken](../../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)
2. [Tenant-en App-ID-waarden ophalen voor aanmelden](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)

Hier volgt een voor beeld van een scherm opname met de registratie van de toepassing.

:::image type="content" source="./media/assign-roles-azure-service-principals/register-an-application.png" alt-text="Scherm opname van het registreren van een toepassing." lightbox="./media/assign-roles-azure-service-principals/register-an-application.png" :::

### <a name="find-your-spn-and-tenant-id"></a>Uw SPN en Tenant-ID zoeken

U hebt ook de object-ID van de SPN en de Tenant-ID van de app nodig. U hebt de informatie nodig voor bewerkingen voor machtigings toewijzing in latere secties.

U kunt de Tenant-ID van de Azure AD-app vinden op de overzichts pagina voor de toepassing. Als u dit wilt vinden in de Azure Portal, gaat u naar Azure Active Directory en selecteert u **bedrijfs toepassingen**. Zoek de app.

:::image type="content" source="./media/assign-roles-azure-service-principals/enterprise-application.png" alt-text="Scherm afbeelding met een voor beeld van een zakelijke toepassing." lightbox="./media/assign-roles-azure-service-principals/enterprise-application.png" :::

Selecteer de app. Hier volgt een voor beeld van de toepassings-ID en de object-ID.

:::image type="content" source="./media/assign-roles-azure-service-principals/application-id-object-id.png" alt-text="Scherm afbeelding met een toepassings-ID en object-ID voor een bedrijfs toepassing." lightbox="./media/assign-roles-azure-service-principals/application-id-object-id.png" :::

U kunt de Tenant-ID vinden op de pagina overzicht van Microsoft Azure AD.

:::image type="content" source="./media/assign-roles-azure-service-principals/tenant-id.png" alt-text="Scherm afbeelding die laat zien waar uw Tenant-ID wordt weer gegeven." lightbox="./media/assign-roles-azure-service-principals/tenant-id.png" :::

Uw Principal Tenant-ID wordt ook aangeduid als Principal-ID, SPN en object-ID op verschillende locaties. De waarde van uw Azure AD-Tenant-ID ziet eruit als een GUID met de volgende indeling: `11111111-1111-1111-1111-111111111111` .

## <a name="permissions-that-can-be-assigned-to-the-spn"></a>Machtigingen die kunnen worden toegewezen aan de SPN

Voor de volgende stappen geeft u machtigingen voor de Azure AD-app om acties uit te voeren met behulp van een EA-rol. U kunt alleen de volgende rollen aan de SPN toewijzen. De roldefinitie-ID, precies zoals weer gegeven, wordt later in toewijzings stappen gebruikt.

| Rol | Acties toegestaan | Roldefinitie-ID |
| --- | --- | --- |
| EnrollmentReader | Het gebruik en de kosten voor alle accounts en abonnementen kunnen worden weer gegeven. Kan de Azure-voor uitbetaling (eerder de monetaire toezeg ging) saldo van de inschrijving bekijken. | 24f8edb6-1668-4659-b5e2-40bb5f3a7d7e |
| DepartmentReader | Down load de gebruiks gegevens voor de afdeling die ze beheert. Kan het gebruik en de kosten weer geven die aan hun afdeling zijn gekoppeld. | db609904-a47f-4794-9be8-9bd86fbffd8a |
| SubscriptionCreator | Nieuwe abonnementen maken in het opgegeven bereik van het account. | a0bcee42-bf30-4d1b-926a-48d21664ef71 |

- Een registratie lezer kan alleen worden toegewezen aan een SPN door een gebruiker met de rol van inschrijvings schrijver.
- Een afdelings lezer kan alleen worden toegewezen aan een SPN door een gebruiker met de rol van inschrijvings schrijver of afdelings schrijver.
- Een maker van een abonnement kan alleen worden toegewezen aan een SPN door een gebruiker die de account eigenaar van het inschrijvings account is.

## <a name="assign-enrollment-account-role-permission-to-the-spn"></a>Een machtiging voor de rol inschrijvings account toewijzen aan de SPN

Lees de [roltoewijzingen](/rest/api/billing/2019-10-01-preview/roleassignments/put) om rest API artikel te plaatsen.

Bij het lezen van het artikel, selecteert u **proberen** het te beginnen met behulp van de SPN.

:::image type="content" source="./media/assign-roles-azure-service-principals/put-try-it.png" alt-text="Scherm afbeelding met de optie try it in het artikel put." lightbox="./media/assign-roles-azure-service-principals/put-try-it.png" :::

Meld u aan met uw account in de Tenant die toegang heeft tot de inschrijving waarvoor u toegang wilt toewijzen.

Geef de volgende para meters op als onderdeel van de API-aanvraag.

**billingAccountName**

De para meter is de ID van het facturerings account. U vindt deze in de Azure Portal op de overzichts pagina Cost Management + facturering.

:::image type="content" source="./media/assign-roles-azure-service-principals/billing-account-id.png" alt-text="Scherm opname met de ID van het facturerings account." lightbox="./media/assign-roles-azure-service-principals/billing-account-id.png" :::

**billingRoleAssignmentName**

De para meter is een unieke GUID die u moet opgeven. U kunt een GUID genereren met behulp van de [nieuwe-GUID](/powershell/module/microsoft.powershell.utility/new-guid?view=powershell-7.1&preserve-view=true) Power shell-opdracht.

U kunt ook de website van de [online-GUID/UUID-Generator](https://guidgenerator.com/) gebruiken om een unieke GUID te genereren.

**API-versie**

Gebruik de **2019-10-01-preview-** versie.

De aanvraag tekst heeft JSON-code die u moet gebruiken.

Gebruik de voorbeeld aanvraag tekst op [roltoewijzingen-voor beelden](/rest/api/billing/2019-10-01-preview/roleassignments/put#examples).

Er zijn drie para meters die u moet gebruiken als onderdeel van de JSON.

| Parameter | Waar u het kunt vinden |
| --- | --- |
| Eigenschappen. principalId | Zie [uw SPN en Tenant-id zoeken](#find-your-spn-and-tenant-id). |
| Eigenschappen. principalTenantId | Zie [uw SPN en Tenant-id zoeken](#find-your-spn-and-tenant-id). |
| Eigenschappen. Roledefinitionid hebben | "/providers/Microsoft.Billing/billingAccounts/{BillingAccountName}/billingRoleDefinitions/24f8edb6-1668-4659-b5e2-40bb5f3a7d7e" |

De naam van het facturerings account is dezelfde para meter die u hebt gebruikt in de API-para meters. Het is de registratie-ID die u ziet in de EA-Portal en Azure Portal.

U ziet dat `24f8edb6-1668-4659-b5e2-40bb5f3a7d7e` de definitie-id van een facturerings functie voor een EnrollmentReader.

Selecteer **uitvoeren** om de opdracht te starten.

:::image type="content" source="./media/assign-roles-azure-service-principals/roleassignments-put-try-it-run.png" alt-text="Scherm opname van een voor beeld van een roltoewijzing. Probeer het opnieuw met voorbeeld gegevens die u kunt uitvoeren." lightbox="./media/assign-roles-azure-service-principals/roleassignments-put-try-it-run.png" :::

Er `200 OK` wordt een antwoord weer gegeven dat de SPN is toegevoegd.

U kunt nu de SPN (Azure AD-app met de object-ID) gebruiken om op een geautomatiseerde manier toegang te krijgen tot EA-Api's. De SPN heeft de EnrollmentReader-rol.

## <a name="assign-the-department-reader-role-to-the-spn"></a>De rol van de afdelings lezer toewijzen aan de SPN

Lees, voordat u begint, de toewijzing van de [rol inschrijvings afdeling-](/rest/api/billing/2019-10-01-preview/enrollmentdepartmentroleassignments/put) rest API artikel.

Selecteer bij het lezen van het artikel **proberen het**.

:::image type="content" source="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it.png" alt-text="Scherm afbeelding van de optie Probeer het opnieuw in het artikel functie toewijzingen van de inschrijvings afdeling." lightbox="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it.png" :::

Meld u aan met uw account in de Tenant die toegang heeft tot de inschrijving waarvoor u toegang wilt toewijzen.

Geef de volgende para meters op als onderdeel van de API-aanvraag.

**billingAccountName**

Het is de ID van het facturerings account. U vindt deze in de Azure Portal op de overzichts pagina Cost Management + facturering.

:::image type="content" source="./media/assign-roles-azure-service-principals/billing-account-id.png" alt-text="Scherm opname met de ID van het facturerings account." lightbox="./media/assign-roles-azure-service-principals/billing-account-id.png" :::

**billingRoleAssignmentName**

De para meter is een unieke GUID die u moet opgeven. U kunt een GUID genereren met behulp van de [nieuwe-GUID](/powershell/module/microsoft.powershell.utility/new-guid?view=powershell-7.1&preserve-view=true) Power shell-opdracht.

U kunt ook de website van de [online-GUID/UUID-Generator](https://guidgenerator.com/) gebruiken om een unieke GUID te genereren.

**departmentName**

Het is de afdelings-ID. U kunt afdelings-Id's weer geven in de Azure Portal. Ga naar Cost Management en facturering > **afdelingen**.

Voor dit voor beeld hebben we de ACE-afdeling gebruikt. De ID voor het voor beeld is `84819` .

:::image type="content" source="./media/assign-roles-azure-service-principals/department-id.png" alt-text="Scherm afbeelding met een voorbeeld afdelings-ID." lightbox="./media/assign-roles-azure-service-principals/department-id.png" :::

**API-versie**

Gebruik de **2019-10-01-preview-** versie.

De aanvraag tekst heeft JSON-code die u moet gebruiken.

Gebruik het voor beeld op het [inschrijvings kostenplaats rollen-put](/billing/2019-10-01-preview/enrollmentdepartmentroleassignments/put). Er zijn drie para meters die u moet gebruiken als onderdeel van de JSON.

| Parameter | Waar u het kunt vinden |
| --- | --- |
| Eigenschappen. principalId | Zie [uw SPN en Tenant-id zoeken](#find-your-spn-and-tenant-id). |
| Eigenschappen. principalTenantId | Zie [uw SPN en Tenant-id zoeken](#find-your-spn-and-tenant-id). |
| Eigenschappen. Roledefinitionid hebben | "/providers/Microsoft.Billing/billingAccounts/{BillingAccountName}/billingRoleDefinitions/db609904-a47f-4794-9be8-9bd86fbffd8a" |

De naam van het facturerings account is dezelfde para meter die u hebt gebruikt in de API-para meters. Het is de registratie-ID die u ziet in de EA-Portal en Azure Portal.

De definitie-ID van de facturerings functie `db609904-a47f-4794-9be8-9bd86fbffd8a` is voor een afdelings lezer.

Selecteer **uitvoeren** om de opdracht te starten.

:::image type="content" source="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it-run.png" alt-text="Scherm opname van een voor beeld van een toewijzing van een inschrijvings afdeling – plaats de REST met de voorbeeld gegevens die u kunt uitvoeren." lightbox="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it-run.png" :::

Er `200 OK` wordt een antwoord weer gegeven dat de SPN is toegevoegd.

U kunt nu de SPN (Azure AD-app met de object-ID) gebruiken om op een geautomatiseerde manier toegang te krijgen tot EA-Api's. De SPN heeft de DepartmentReader-rol.

## <a name="assign-the-subscription-creator-role-to-the-spn"></a>De rol van de maker van het abonnement toewijzen aan de SPN

Lees de [functie toewijzingen inschrijvings account-artikel plaatsen](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put) .

Selecteer bij het lezen de functie **try** -out om de rol van het abonnement Maker toe te wijzen aan de SPN.

:::image type="content" source="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it.png" alt-text="Scherm afbeelding van de optie Probeer het opnieuw in het artikel functie toewijzingen van het inschrijvings account." lightbox="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it.png" :::

Meld u aan met uw account in de Tenant die toegang heeft tot de inschrijving waarvoor u toegang wilt toewijzen.

Geef de volgende para meters op als onderdeel van de API-aanvraag. Lees het artikel op de [functie toewijzingen van het inschrijvings account-para meters voor put-URI](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put#uri-parameters).

**billingAccountName**

De para meter is de ID van het facturerings account. U vindt deze in de Azure Portal op de overzichts pagina Cost Management + facturering.

:::image type="content" source="./media/assign-roles-azure-service-principals/billing-account-id.png" alt-text="Scherm opname met de ID van het facturerings account." lightbox="./media/assign-roles-azure-service-principals/billing-account-id.png" :::

**billingRoleAssignmentName**

De para meter is een unieke GUID die u moet opgeven. U kunt een GUID genereren met behulp van de [nieuwe-GUID](/powershell/module/microsoft.powershell.utility/new-guid?view=powershell-7.1&preserve-view=true) Power shell-opdracht.

U kunt ook de website van de [online-GUID/UUID-Generator](https://guidgenerator.com/) gebruiken om een unieke GUID te genereren.
**enrollmentAccountName**

De para meter is de account-ID. Zoek de account-ID voor de account naam in de Azure Portal in Cost Management en facturering in het gebied inschrijving en afdeling.

Voor dit voor beeld hebben we het test account GTM gebruikt. De ID is `196987` .

:::image type="content" source="./media/assign-roles-azure-service-principals/account-id.png" alt-text="Scherm opname met de account-ID." lightbox="./media/assign-roles-azure-service-principals/account-id.png" :::

**API-versie**

Gebruik de **2019-10-01-preview-** versie.

De aanvraag tekst heeft JSON-code die u moet gebruiken.

Gebruik het voor beeld op het [inschrijvings kostenplaats rollen-opslag-voor beelden](/rest/api/billing/2019-10-01-preview/enrollmentdepartmentroleassignments/put#putenrollmentdepartmentadministratorroleassignment).

Er zijn drie para meters die u moet gebruiken als onderdeel van de JSON.

| Parameter | Waar u het kunt vinden |
| --- | --- |
| Eigenschappen. principalId | Zie [uw SPN en Tenant-id zoeken](#find-your-spn-and-tenant-id). |
| Eigenschappen. principalTenantId | Zie [uw SPN en Tenant-id zoeken](#find-your-spn-and-tenant-id). |
| Eigenschappen. Roledefinitionid hebben | "/providers/Microsoft.Billing/billingAccounts/{BillingAccountID}/enrollmentAccounts/196987/billingRoleDefinitions/a0bcee42-bf30-4d1b-926a-48d21664ef71" |

De naam van het facturerings account is dezelfde para meter die u hebt gebruikt in de API-para meters. Het is de registratie-ID die u ziet in de EA-Portal en Azure Portal.

De definitie-ID van de facturerings functie `a0bcee42-bf30-4d1b-926a-48d21664ef71` is voor de rol van de maker van het abonnement.

Selecteer **uitvoeren** om de opdracht te starten.

:::image type="content" source="./media/assign-roles-azure-service-principals/enrollment-account-role-assignments-put-try-it.png" alt-text="Scherm afbeelding van de optie Probeer het opnieuw in de toewijzing van de functie inschrijvings account-artikel plaatsen" lightbox="./media/assign-roles-azure-service-principals/enrollment-account-role-assignments-put-try-it.png" :::

Er `200 OK` wordt een antwoord weer gegeven dat de SPN is toegevoegd.

U kunt nu de SPN (Azure AD-app met de object-ID) gebruiken om op een geautomatiseerde manier toegang te krijgen tot EA-Api's. De SPN heeft de SubscriptionCreator-rol.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [beheer van Azure EA-portals](ea-portal-administration.md).