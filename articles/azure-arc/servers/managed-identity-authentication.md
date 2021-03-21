---
title: Verifiëren met Azure-resources met servers waarop Arc is ingeschakeld
description: In dit artikel wordt de ondersteuning voor Azure Instance Metadata Service voor Arc-servers beschreven en wordt uitgelegd hoe u met behulp van een geheim kunt verifiëren met Azure-resources en lokale.
ms.topic: conceptual
ms.date: 12/09/2020
ms.openlocfilehash: 49b70928ae972da8e0a0d14d711e4b6f246cca6a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96939102"
---
# <a name="authenticate-against-azure-resources-with-arc-enabled-servers"></a>Verifiëren met Azure-resources met servers waarop Arc is ingeschakeld

Toepassingen of processen die rechtstreeks worden uitgevoerd op een Azure Arc-server kunnen beheerde identiteiten gebruiken om toegang te krijgen tot andere Azure-resources die ondersteuning bieden voor op Azure Active Directory gebaseerde verificatie. Een toepassing kan een [toegangs token](../../active-directory/develop/developer-glossary.md#access-token) verkrijgen waarmee de identiteit wordt aangegeven, die door het systeem wordt toegewezen voor servers die zijn ingeschakeld voor Arc en die worden gebruikt als een Bearer-token om zichzelf te verifiëren bij een andere service.

Raadpleeg de documentatie van het [overzicht van beheerde identiteit](../../active-directory/managed-identities-azure-resources/overview.md) voor een gedetailleerde beschrijving van beheerde identiteiten, evenals het onderscheid tussen systeem toewijzingen en door de gebruiker toegewezen identiteiten.

In dit artikel laten we zien hoe een server een door het systeem toegewezen beheerde identiteit kan gebruiken om toegang te krijgen tot Azure [Key Vault](../../key-vault/general/overview.md). Key Vault fungeert als een bootstrap en maakt het mogelijk voor uw clienttoepassing om met een geheim toegang te krijgen tot resources die niet zijn beveiligd met Azure Active Directory (AD). TLS/SSL-certificaten die worden gebruikt door uw IIS-webservers kunnen bijvoorbeeld worden opgeslagen in Azure Key Vault en de certificaten veilig implementeren op Windows-of Linux-servers buiten Azure.

## <a name="security-overview"></a>Beveiligingsoverzicht

Tijdens de onboarding van uw server naar servers met Azure-Arc, worden er diverse acties uitgevoerd om te configureren met behulp van een beheerde identiteit, vergelijkbaar met wat er voor een Azure-VM wordt uitgevoerd:

- Azure Resource Manager ontvangt een aanvraag om de door het systeem toegewezen beheerde identiteit in te scha kelen op de met Arc ingeschakelde server.

- Azure Resource Manager maakt een Service-Principal in azure AD voor de identiteit van de server. De service-principal wordt gemaakt in de Azure AD-tenant die wordt vertrouwd door het abonnement.

- Azure Resource Manager configureert de identiteit op de server door het identiteits eindpunt van Azure Instance Metadata Service (IMDS) voor [Windows](../../virtual-machines/windows/instance-metadata-service.md) of [Linux](../../virtual-machines/linux/instance-metadata-service.md) bij te werken met de client-id en het certificaat van de Service-Principal. Het eind punt is een REST eindpunt dat alleen toegankelijk is vanaf de server met een bekend, niet-routeerbaar IP-adres. Deze service biedt een subset van meta gegevens over de door Arc ingeschakelde server om deze te beheren en te configureren.

De omgeving van een server met beheerde identiteiten wordt geconfigureerd met de volgende variabelen op een Windows-Server met Arc-functionaliteit:

- **IMDS_ENDPOINT**: het IP-adres van het IMDS-eind punt `http://localhost:40342` voor servers met Arc-functionaliteit.

- **IDENTITY_ENDPOINT**: het localhost-eind punt dat overeenkomt met de beheerde identiteit van de service `http://localhost:40342/metadata/identity/oauth2/token` .

De code die op de server wordt uitgevoerd, kan een token aanvragen bij het Azure instance meta data service-eind punt dat alleen toegankelijk is vanaf de server.

De variabele voor de systeem omgeving **IDENTITY_ENDPOINT** wordt gebruikt om het identiteits eindpunt te detecteren op basis van toepassingen. Toepassingen moeten proberen om **IDENTITY_ENDPOINT** -en **IMDS_ENDPOINT** waarden op te halen en deze te gebruiken. Toepassingen met elk toegangs niveau mogen aanvragen voor de eind punten indienen. Meta gegevens antwoorden worden verwerkt als normaal en worden toegewezen aan elk proces op de computer. Als er echter een aanvraag wordt ingediend die een token zou openbaren, moet de client een geheim opgeven om te bevestigen dat ze toegang hebben tot gegevens die alleen beschikbaar zijn voor gebruikers met een hogere bevoegdheid.

## <a name="prerequisites"></a>Vereisten

- Inzicht in beheerde identiteiten.
- Een server die is verbonden en geregistreerd met servers waarop Arc is ingeschakeld.
- U bent lid van de [groep eigenaar](../../role-based-access-control/built-in-roles.md#owner)* * in het abonnement of de resource groep om de vereiste stappen voor het maken van resources en het beheer van rollen uit te voeren.
- Een Azure Key Vault om uw referentie op te slaan en op te halen. en wijs de toegang tot de Azure Arc-identiteit toe aan de sleutel kluis.

    - Als u geen Key Vault hebt gemaakt, raadpleegt u [Key Vault maken](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad.md#create-a-key-vault-).
    - Als u toegang wilt configureren door de beheerde identiteit die wordt gebruikt door de-server, raadpleegt u [toegang verlenen voor Linux](../../active-directory/managed-identities-azure-resources/tutorial-linux-vm-access-nonaad.md#grant-access) of [toegang verlenen voor Windows](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad.md#grant-access). Voor stap nummer 5 voert u de naam van de ingeschakelde Arc-server in. Zie [een toegangs beleid toewijzen met behulp van Power shell](../../key-vault/general/assign-access-policy-powershell.md)om dit te volt ooien met behulp van Power shell.

## <a name="acquiring-an-access-token-using-rest-api"></a>Een toegangs Token ophalen met behulp van REST API

De methode voor het verkrijgen en gebruiken van een door het systeem toegewezen beheerde identiteit om te verifiëren met Azure-resources, is vergelijkbaar met de manier waarop deze wordt uitgevoerd met een Azure VM.

Voor een Windows Server met Arc-functionaliteit roept u met behulp van Power shell de webaanvraag aan om het token van de lokale host in de specifieke poort op te halen. Geef de aanvraag op met behulp van het IP-adres of de omgevings variabele **IDENTITY_ENDPOINT**.

```powershell
$apiVersion = "2020-06-01"
$resource = "https://management.azure.com/"
$endpoint = "{0}?resource={1}&api-version={2}" -f $env:IDENTITY_ENDPOINT,$resource,$apiVersion
$secretFile = ""
try
{
    Invoke-WebRequest -Method GET -Uri $endpoint -Headers @{Metadata='True'} -UseBasicParsing
}
catch
{
    $wwwAuthHeader = $_.Exception.Response.Headers["WWW-Authenticate"]
    if ($wwwAuthHeader -match "Basic realm=.+")
    {
        $secretFile = ($wwwAuthHeader -split "Basic realm=")[1]
    }
}
Write-Host "Secret file path: " $secretFile`n
$secret = cat -Raw $secretFile
$response = Invoke-WebRequest -Method GET -Uri $endpoint -Headers @{Metadata='True'; Authorization="Basic $secret"} -UseBasicParsing
if ($response)
{
    $token = (ConvertFrom-Json -InputObject $response.Content).access_token
    Write-Host "Access token: " $token
}
```

Het volgende antwoord is een voor beeld dat wordt geretourneerd:

:::image type="content" source="media/managed-identity-authentication/powershell-token-output-example.png" alt-text="Het ophalen van het toegangs token met behulp van Power shell is geslaagd.":::

Voor een met Arc ingeschakelde Linux-server roept u met bash de webaanvraag aan om het token van de lokale host op te halen in de specifieke poort. Geef de volgende aanvraag op met behulp van het IP-adres of de omgevings variabele **IDENTITY_ENDPOINT**. U hebt een SSH-client nodig om deze stap te volt ooien.

```bash
ChallengeTokenPath=$(curl -s -D - -H Metadata:true "http://127.0.0.1:40342/metadata/identity/oauth2/token?api-version=2019-11-01&resource=https%3A%2F%2Fmanagement.azure.com" | grep Www-Authenticate | cut -d "=" -f 2 | tr -d "[:cntrl:]")
ChallengeToken=$(cat $ChallengeTokenPath)
if [ $? -ne 0 ]; then
    echo "Could not retrieve challenge token, double check that this command is run with root privileges."
else
    curl -s -H Metadata:true -H "Authorization: Basic $ChallengeToken" "http://127.0.0.1:40342/metadata/identity/oauth2/token?api-version=2019-11-01&resource=https%3A%2F%2Fmanagement.azure.com"
fi
```

Het volgende antwoord is een voor beeld dat wordt geretourneerd:

:::image type="content" source="media/managed-identity-authentication/bash-token-output-example.png" alt-text="Het ophalen van het toegangs token met behulp van bash is geslaagd.":::

Het antwoord bevat het toegangs token dat u nodig hebt om toegang te krijgen tot een resource in Azure. Als u de configuratie wilt volt ooien om te verifiëren bij Azure Key Vault, raadpleegt u [Key Vault met Windows](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad.md#access-data) of [toegang tot Key Vault met Linux](../../active-directory/managed-identities-azure-resources/tutorial-linux-vm-access-nonaad.md#access-data).

## <a name="next-steps"></a>Volgende stappen

- Zie [Key Vault Overview](../../key-vault/general/overview.md)voor meer informatie over Azure Key Vault.

- Meer informatie over het toewijzen van beheerde identiteits toegang aan een resource [met behulp van Power shell](../../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md) of het gebruik van [de Azure cli](../../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md).