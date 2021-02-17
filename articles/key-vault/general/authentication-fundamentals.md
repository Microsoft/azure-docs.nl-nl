---
title: Basis beginselen van Azure Key Vault-verificatie
description: Meer informatie over de werking van het verificatie model van de sleutel kluis
author: ShaneBala-keyvault
ms.author: sudbalas
ms.date: 09/25/2020
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.openlocfilehash: a75ecac20c553f697585648c5654d375b85e5183
ms.sourcegitcommit: b513b0becf878eb9a1554c26da53aa48d580bb22
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100535192"
---
# <a name="key-vault-authentication-fundamentals"></a>Basisprincipes van Key Vault-verificatie

Met Azure Key Vault kunt u toepassings referenties zoals geheimen, sleutels en certificaten veilig opslaan en beheren in een centrale en beveiligde Cloud opslagplaats. Key Vault elimineert de nood zaak om referenties op te slaan in uw toepassingen. Uw toepassingen kunnen tijdens de uitvoering verifiëren Key Vault om referenties op te halen.

Als beheerder kunt u nauw keurig bepalen welke gebruikers en toepassingen toegang hebben tot uw sleutel kluis en kunt u de bewerkingen die ze uitvoeren, beperken en controleren. Dit document bevat uitleg over de basis concepten van het toegangs model voor de sleutel kluis. Het biedt u een inleidend kennis niveau en laat zien hoe u een gebruiker of toepassing kunt verifiëren bij de sleutel kluis van begin tot eind.

## <a name="required-knowledge"></a>Vereiste kennis

In dit document wordt ervan uitgegaan dat u bekend bent met de volgende concepten. Als u niet bekend bent met een van deze concepten, volgt u de Help-koppelingen voordat u doorgaat.

* [Koppeling](../../active-directory/fundamentals/active-directory-whatis.md) Azure Active Directory
* [Koppeling](./authentication.md#app-identity-and-security-principals) voor beveiligings-principals

## <a name="key-vault-configuration-steps-summary"></a>Samen vatting van Key Vault configuratie stappen

1. Registreer uw gebruiker of toepassing in Azure Active Directory als een beveiligingsprincipal.
1. Configureer een roltoewijzing voor uw beveiligingsprincipal in Azure Active Directory.
1. Configureer het toegangs beleid voor de sleutel kluis voor uw beveiligings-principal.
1. Key Vault firewall toegang tot uw sleutel kluis configureren (optioneel).
1. Test de mogelijkheid van uw beveiligingsprincipal om toegang te krijgen tot de sleutel kluis.

## <a name="register-a-user-or-application-in-azure-active-directory-as-a-security-principal"></a>Een gebruiker of toepassing in Azure Active Directory registreren als een beveiligingsprincipal

Wanneer een gebruiker of toepassing een aanvraag indient voor de sleutel kluis, moet de aanvraag eerst worden geverifieerd door Azure Active Directory. Om dit te laten werken, moet de gebruiker of toepassing worden geregistreerd in Azure Active Directory als een beveiligingsprincipal.

Volg de onderstaande documentatie koppelingen om inzicht te krijgen in het registreren van een gebruiker of toepassing in Azure Active Directory.
**Zorg ervoor dat u een wacht woord voor de gebruikers registratie en een client geheim of referentie voor het client certificaat voor toepassingen maakt.**

* Een gebruiker registreren bij Azure Active Directory [koppeling](../../active-directory/fundamentals/add-users-azure-active-directory.md)
* Een toepassing registreren in Azure Active Directory [koppeling](../../active-directory/develop/quickstart-register-app.md)

## <a name="assign-your-security-principal-a-role"></a>Uw beveiligingsprincipal een rol toewijzen

U kunt Azure RBAC (op rollen gebaseerd toegangs beheer) gebruiken om machtigingen toe te wijzen aan beveiligings-principals. Deze machtigingen worden roltoewijzingen genoemd.

In de context van sleutel kluis bepalen deze roltoewijzingen het toegangs niveau van de beveiligingsprincipal voor het beheer vlak (ook wel besturings vlak genoemd) van sleutel kluis. Deze roltoewijzingen bieden niet rechtstreeks toegang tot de gegevens vlak geheimen, maar bieden toegang tot het beheren van eigenschappen van sleutel kluis. Bijvoorbeeld: een gebruiker of toepassing die een **rol van lezer** heeft toegewezen, mag geen wijzigingen aanbrengen in de firewall instellingen van de sleutel kluis, terwijl een gebruiker of toepassing die een **rol Inzender** toegewezen, wijzigingen kan aanbrengen. Geen van beide rollen heeft rechtstreekse toegang voor het uitvoeren van bewerkingen op geheimen, sleutels en certificaten, zoals het maken of ophalen van de waarde tot ze toegang krijgen tot het gegevens vlak van de sleutel kluis. Dit wordt in de volgende stap besproken.

>[!IMPORTANT]
> Hoewel gebruikers met de rol Inzender of eigenaar geen toegang hebben tot het uitvoeren van bewerkingen op geheimen die zijn opgeslagen in de sleutel kluis, hebben de rollen Inzender en eigenaar, machtigingen voor het toevoegen of verwijderen van toegangs beleid aan geheimen die zijn opgeslagen in de sleutel kluis. Daarom kunnen gebruikers met deze roltoewijzingen zichzelf toegang verlenen tot toegangs geheimen in de sleutel kluis. Daarom is het raadzaam dat alleen beheerders toegang hebben tot de rollen Inzender of eigenaar. Gebruikers en toepassingen die alleen geheimen moeten ophalen uit de sleutel kluis, moeten de rol van lezer krijgen. **Meer informatie vindt u in de volgende sectie.**

>[!NOTE]
> Wanneer u een roltoewijzing toewijst aan een gebruiker op het Azure Active Directory Tenant niveau, wordt deze set machtigingen trickle tot alle abonnementen, resource groepen en resources binnen het bereik van de toewijzing. Als u de principal van de minimale bevoegdheid wilt volgen, kunt u deze roltoewijzing op een nauw keuriger bereik maken. U kunt bijvoorbeeld een gebruiker een rol van lezer toewijzen op het abonnements niveau en een rol van eigenaar voor een enkele sleutel kluis. Ga naar de instellingen voor Identity Access Management (IAM) van een abonnement, een resource groep of een sleutel kluis om een roltoewijzing te maken op een nauw keuriger bereik.

* Meer informatie over de [koppeling](../../role-based-access-control/built-in-roles.md) van Azure-functies
* Meer informatie over [koppeling](../../role-based-access-control/role-assignments-portal.md) van roltoewijzingen toewijzen of verwijderen

## <a name="configure-key-vault-access-policies-for-your-security-principal"></a>Een sleutel kluis toegangs beleid configureren voor uw beveiligings-principal

Voordat u toegang verleent aan uw gebruikers en toepassingen voor toegang tot de sleutel kluis, is het belang rijk om te begrijpen wat de verschillende typen bewerkingen zijn die kunnen worden uitgevoerd op een sleutel kluis. Er zijn twee hoofd typen van sleutel kluis bewerkingen, beheer vlak (ook wel besturings vlak) en gegevenslaag bewerkingen.

In deze tabel ziet u een aantal voor beelden van de verschillende bewerkingen die worden beheerd door het beheer vlak versus het gegevens vlak. Bewerkingen die de eigenschappen van de sleutel kluis wijzigen, zijn beheer vlak bewerkingen. Bewerkingen die de waarde van geheimen die zijn opgeslagen in de sleutel kluis wijzigen of ophalen, zijn gegevenslaag bewerkingen.

|Beheer vlak bewerkingen (voor beelden)|Data-vlak bewerkingen (voor beelden)|
| --- | --- |
| Key Vault maken | Een sleutel, geheim, certificaat maken
| Key Vault verwijderen | Een sleutel, geheim, certificaat verwijderen
| Key Vault roltoewijzingen toevoegen of verwijderen | Waarden van sleutels, geheimen en certificaten weer geven en ophalen
| Key Vault toegangs beleid toevoegen of verwijderen | Back-up-en herstel sleutels, geheimen, certificaten
| Firewall instellingen van Key Vault wijzigen | Sleutels, geheimen, certificaten vernieuwen
| Key Vault herstel instellingen wijzigen | Tijdelijke, Verwijderde sleutels, geheimen en certificaten verwijderen of herstellen
| Instellingen voor Diagnostische logboeken van Key Vault wijzigen

### <a name="management-plane-access--azure-active-directory-role-assignments"></a>Toegangs & voor beheer vlak Azure Active Directory roltoewijzingen

Azure Active Directory roltoewijzingen verlenen toegang om beheer vlak bewerkingen uit te voeren op een sleutel kluis. Deze toegang wordt doorgaans verleend aan gebruikers en niet op toepassingen. U kunt beperken welk beheer vlak bewerkingen een gebruiker kan uitvoeren door de roltoewijzing van een gebruiker te wijzigen.

Als u bijvoorbeeld een gebruiker een Key Vault lezer-rol toewijst aan een gebruiker, kunnen ze de eigenschappen van de sleutel kluis zien, zoals het toegangs beleid, maar kunnen ze geen wijzigingen aanbrengen. Als u een gebruiker toewijst, biedt de rol eigenaar volledige toegang tot de instellingen voor het beheer van sleutel kluis.

Roltoewijzingen worden beheerd op de Blade van de sleutel kluis Access Control (IAM). Als u een bepaalde gebruiker toegang wilt geven tot een lezer of als beheerder van meerdere sleutel kluis resources, kunt u een roltoewijzing maken op basis van de kluis, de resource groep of het abonnements niveau, en de roltoewijzing wordt toegevoegd aan alle resources binnen het bereik van de toewijzing.

Toegang tot gegevenslaag of toegang tot het uitvoeren van bewerkingen voor sleutels, geheimen en certificaten die zijn opgeslagen in de sleutel kluis kunnen op een van de volgende twee manieren worden toegevoegd.

### <a name="data-plane-access-option-1-classic-key-vault-access-policies"></a>Toegangs optie voor gegevenslaag 1: klassiek Key Vault-toegangs beleid

Met het toegangs beleid voor sleutel kluizen kunnen gebruikers en toepassingen toegang krijgen tot gegevens vlak bewerkingen op een sleutel kluis.

> [!NOTE]
> Dit toegangs model is niet compatibel met Azure RBAC voor sleutel kluis (optie 2) zoals hieronder wordt beschreven. U moet er een kiezen. U krijgt de mogelijkheid om deze selectie te maken wanneer u op het tabblad toegangs beleid van uw sleutel kluis klikt.

Klassiek toegangs beleid is nauw keurig. Dit betekent dat u de mogelijkheid van elke afzonderlijke gebruiker of toepassing voor het uitvoeren van afzonderlijke bewerkingen binnen een sleutel kluis kunt toestaan of weigeren. Enkele voorbeelden:

* Met beveiligingsprincipal 1 kan elke sleutel bewerking worden uitgevoerd, maar het is niet toegestaan een geheim-of certificaat bewerking uit te voeren.
* Met beveiligingsprincipal 2 kunnen alle sleutels, geheimen en certificaten worden weer geven en gelezen, maar kunnen geen bewerkingen voor maken, verwijderen of vernieuwen worden uitgevoerd.
* Met beveiligingsprincipal 3 kan een back-up worden gemaakt en kunnen alle geheimen worden hersteld, maar kan de waarde van de geheimen zelf niet worden gelezen.

Klassiek toegangs beleid staat echter geen machtigingen op niveau per object toe en toegewezen machtigingen worden toegepast op het bereik van een individuele sleutel kluis. Als u bijvoorbeeld de machtiging ' geheim Get ' toegangs beleid toewijst aan een beveiligingsprincipal in een bepaalde sleutel kluis, heeft de beveiligingsprincipal de mogelijkheid om alle geheimen binnen die specifieke sleutel kluis op te halen. De machtiging ' geheim ophalen ' wordt echter niet automatisch uitgebreid naar andere sleutel kluizen en moet expliciet worden toegewezen.

> [!IMPORTANT]
> De klassieke sleutel kluis toegangs beleid en Azure Active Directory roltoewijzingen zijn onafhankelijk van elkaar. Als u een beveiligingsprincipal toewijst voor een rol Inzender op abonnements niveau, wordt de beveiligingsprincipal niet automatisch toegestaan op elke sleutel kluis binnen het bereik van het abonnement de mogelijkheid om gegevenslaag bewerkingen uit te voeren. De beveiligingsprincipal moet nog steeds worden verleend, of u hebt zichzelf toegangs beleid machtigingen voor het uitvoeren van gegevenslaag bewerkingen.

### <a name="data-plane-access-option-2--azure-rbac-for-key-vault-preview"></a>Access-optie voor gegevens vlak 2: Azure RBAC voor Key Vault (preview-versie)

Een nieuwe manier om toegang tot het sleutel kluis-gegevens vlak te verlenen, is via Azure op rollen gebaseerd toegangs beheer (Azure RBAC) voor sleutel kluis.

> [!NOTE]
> Dit toegangs model is niet compatibel met het klassieke toegangs beleid van Key kluis dat hierboven wordt weer gegeven. U moet er een kiezen. U krijgt de mogelijkheid om deze selectie te maken wanneer u op het tabblad toegangs beleid van uw sleutel kluis klikt.

Key Vault roltoewijzingen zijn een set van ingebouwde rollen toewijzingen van Azure die algemene sets machtigingen bevatten die worden gebruikt voor toegang tot sleutels, geheimen en certificaten. Dit machtigings model biedt ook aanvullende mogelijkheden die niet beschikbaar zijn in het klassieke sleutel kluis toegangs beleid model.

* U kunt Azure RBAC-machtigingen op schaal beheren door gebruikers toe te staan deze rollen toe te wijzen aan een abonnement, resource groep of een kluis niveau van individuele sleutels. Een gebruiker beschikt over de machtigingen voor het gegevens vlak van alle sleutel kluizen binnen het bereik van de RBAC-toewijzing van Azure. Dit elimineert de nood zaak om afzonderlijke toegangs beleid machtigingen per gebruiker/toepassing per sleutel kluis toe te wijzen.

* De Azure RBAC-machtigingen zijn compatibel met Privileged Identity Management of PIM. Zo kunt u just-in-time-toegangs beheer configureren voor geprivilegieerde rollen als Key Vault beheerder. Dit is een best practice voor beveiliging en volgt de principal van de minimale bevoegdheid door permanente toegang tot uw sleutel kluizen te elimineren.

Raadpleeg de volgende documenten voor meer informatie over Azure RBAC voor Key Vault:

* [Koppeling](./secure-your-key-vault.md#management-plane-and-azure-rbac) Azure RBAC voor Key Vault
* [Koppeling](../../role-based-access-control/built-in-roles.md#key-vault-administrator) van Azure RBAC voor Key Vault-rollen

## <a name="configure-key-vault-firewall"></a>Key Vault firewall configureren

Standaard staat sleutel kluis verkeer van het open bare Internet toe om uw sleutel kluis via een openbaar eind punt te verzenden. Voor een extra beveiligingslaag kunt u de firewall van Azure Key Vault zo configureren dat de toegang tot het open bare eind punt van de sleutel kluis wordt beperkt.

Als u de sleutel kluis firewall wilt inschakelen, klikt u op het tabblad netwerk in de sleutel kluis Portal en selecteert u het keuze rondje om toegang toe te staan vanaf: "persoonlijk eind punt en geselecteerde netwerken". Als u ervoor kiest de sleutel kluis firewall in te scha kelen, zijn dit de manieren waarop u verkeer kunt toestaan via de firewall van de sleutel kluis.

* Voeg IPv4-adressen toe aan de lijst met toegestane firewalls voor de sleutel kluis. Deze optie werkt het beste voor toepassingen met een statisch IP-adres.

* Voeg een virtueel netwerk toe aan de firewall voor de sleutel kluis. Deze optie werkt het beste bij Azure-resources met dynamische IP-adressen, zoals Virtual Machines. U kunt Azure-resources toevoegen aan een virtueel netwerk en het virtuele netwerk toevoegen aan de lijst met toegestane firewalls voor de sleutel kluis. Deze optie maakt gebruik van een service-eind punt dat een privé-IP-adres in het virtuele netwerk is. Dit biedt een extra beveiligingslaag, zodat er geen verkeer tussen de sleutel kluis en het virtuele netwerk wordt gerouteerd via het open bare Internet. Raadpleeg de volgende documentatie voor meer informatie over service-eind punten. [gekoppeld](./network-security.md)

* Een persoonlijke koppelings verbinding toevoegen aan de sleutel kluis. Met deze optie wordt het virtuele netwerk rechtstreeks verbonden met een bepaald exemplaar van sleutel kluis, waardoor uw sleutel kluis in het virtuele netwerk effectief wordt. Zie de volgende [koppeling](./private-link-service.md) voor meer informatie over het configureren van een particuliere endpoint-verbinding met sleutel kluis.

## <a name="test-your-service-principals-ability-to-access-key-vault"></a>Test de mogelijkheid van de Service-Principal om toegang te krijgen tot de sleutel kluis

Zodra u alle bovenstaande stappen hebt gevolgd, kunt u geheimen van uw sleutel kluis instellen en ophalen.

### <a name="authentication-process-for-users-examples"></a>Verificatie proces voor gebruikers (voor beelden)

* Gebruikers kunnen zich aanmelden bij de Azure Portal om sleutel kluis te gebruiken. [Quick start voor Key Vault Portal](./quick-create-portal.md)

* Gebruiker kan Azure CLI gebruiken voor het gebruik van sleutel kluis. [Snelstartgids voor Key Vault Azure CLI](./quick-create-cli.md)

* Gebruiker kan Azure PowerShell gebruiken om sleutel kluis te gebruiken. [Key Vault Azure PowerShell Snelstartgids](./quick-create-powershell.md)

### <a name="azure-active-directory-authentication-process-for-applications-or-services-examples"></a>Verificatie proces Azure Active Directory voor toepassingen of services (voor beelden)

* Een toepassing levert een client geheim en een client-ID in een functie om een Azure Active Directory-token op te halen. 

* Een toepassing biedt een certificaat om een Azure Active Directory-token op te halen. 

* Een Azure-resource gebruikt MSI-verificatie om een Azure Active Directory-token op te halen. 

* Meer informatie over MSI-verificatie [koppeling](../../active-directory/managed-identities-azure-resources/overview.md)

### <a name="authentication-process-for-application-python-example"></a>Verificatie proces voor toepassingen (python-voor beeld)

Gebruik het volgende codevoorbeeld om te testen of uw toepassing een geheim kan ophalen uit uw sleutelkluis met behulp van de service-principal die u hebt geconfigureerd.

>[!NOTE]
>Dit voor beeld is alleen voor demonstratie-en test doeleinden. **geen gebruik maken van geheime verificatie van client in productie** Dit is geen veilige ontwerp praktijk. U moet het client certificaat of de MSI-verificatie gebruiken als best practice.

```python
from azure.identity import ClientSecretCredential
from azure.keyvault.secrets import SecretClient

tenant_id = "{ENTER YOUR TENANT ID HERE}"                          ##ENTER AZURE TENANT ID
vault_url = "https://{ENTER YOUR VAULT NAME}.vault.azure.net/"     ##ENTER THE URL OF YOUR KEY VAULT
client_id = "{ENTER YOUR CLIENT ID HERE}"                          ##ENTER THE CLIENT ID OF YOUR SERVICE PRINCIPAL
cert_path = "{ENTER YOUR CLIENT SECRET HERE}"                      ##ENTER THE CLIENT SECRET OF YOUR SERVICE PRINCIPAL

def main():

    #AUTHENTICATION TO Azure Active Directory USING CLIENT ID AND CLIENT CERTIFICATE (GET Azure Active Directory TOKEN)
    token = ClientSecretCredential(tenant_id=tenant_id, client_id=client_id, client_secret=client_secret)

    #AUTHENTICATION TO KEY VAULT PRESENTING Azure Active Directory TOKEN
    client = SecretClient(vault_url=vault_url, credential=token)

    #CALL TO KEY VAULT TO GET SECRET
    #ENTER NAME OF A SECRET STORED IN KEY VAULT
    secret = client.get_secret('{SECRET_NAME}')

    #GET PLAINTEXT OF SECRET
    print(secret.value)

#CALL MAIN()
if __name__ == "__main__":
    main()
```

## <a name="next-steps"></a>Volgende stappen

Raadpleeg het volgende document voor meer informatie over de sleutel kluis verificatie. [Key Vault-verificatie](./authentication.md)
