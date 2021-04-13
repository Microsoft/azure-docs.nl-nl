---
title: Azure Key Vault basisprincipes van verificatie
description: Meer informatie over hoe het verificatiemodel van Key Vault werkt
author: ShaneBala-keyvault
ms.author: sudbalas
ms.date: 09/25/2020
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.openlocfilehash: c43995a8b3a072d98db0ba2c8219694f17e49a26
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363424"
---
# <a name="key-vault-authentication-fundamentals"></a>Basisprincipes van Key Vault-verificatie

Azure Key Vault kunt u toepassingsreferenties zoals geheimen, sleutels en certificaten veilig opslaan en beheren in een centrale en veilige cloudopslagplaats. Key Vault hoeft u geen referenties meer op te slaan in uw toepassingen. Uw toepassingen kunnen tijdens run time Key Vault om referenties op te halen.

Als beheerder kunt u goed bepalen welke gebruikers en toepassingen toegang hebben tot uw sleutelkluis en kunt u de bewerkingen die ze uitvoeren beperken en controleren. In dit document worden de basisconcepten van het toegangsmodel van de sleutelkluis uitgelegd. Het biedt u een inleidend kennisniveau en laat zien hoe u een gebruiker of toepassing van begin tot eind kunt verifiëren bij Key Vault.

## <a name="required-knowledge"></a>Vereiste kennis

In dit document wordt ervan uitgenomen dat u bekend bent met de volgende concepten. Als u niet bekend bent met een van deze concepten, volgt u de Help-koppelingen voordat u doorgaat.

* Azure Active Directory [koppeling](../../active-directory/fundamentals/active-directory-whatis.md)
* Koppeling naar [Beveiligingsprincipa](./authentication.md#app-identity-and-security-principals)

## <a name="key-vault-configuration-steps-summary"></a>overzicht Key Vault configuratiestappen

1. Registreer uw gebruiker of toepassing in Azure Active Directory als een beveiligingsprincipaal.
1. Configureer een roltoewijzing voor uw beveiligingsprincipaal in Azure Active Directory.
1. Configureer toegangsbeleid voor de sleutelkluis voor uw beveiligingsprincipaal.
1. Configureer Key Vault firewalltoegang tot uw sleutelkluis (optioneel).
1. Test de mogelijkheid van uw beveiligingsprincipaal om toegang te krijgen tot de sleutelkluis.

## <a name="register-a-user-or-application-in-azure-active-directory-as-a-security-principal"></a>Een gebruiker of toepassing registreren in Azure Active Directory als een beveiligingsprincipaal

Wanneer een gebruiker of toepassing een aanvraag indient bij de sleutelkluis, moet de aanvraag eerst worden geverifieerd door Azure Active Directory. Om dit te laten werken, moet de gebruiker of toepassing worden geregistreerd in Azure Active Directory als een beveiligingsprincipaal.

Volg de onderstaande documentatiekoppelingen voor meer informatie over het registreren van een gebruiker of toepassing in Azure Active Directory.
**Zorg ervoor dat u een wachtwoord maakt voor gebruikersregistratie en een clientgeheim of clientcertificaatreferenties voor toepassingen.**

* Een gebruiker registreren in Azure Active Directory [koppeling](../../active-directory/fundamentals/add-users-azure-active-directory.md)
* Een toepassing registreren in Azure Active Directory [link](../../active-directory/develop/quickstart-register-app.md)

## <a name="assign-your-security-principal-a-role"></a>Een rol toewijzen aan uw beveiligingsprincipaal

U kunt op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) gebruiken om machtigingen toe te wijzen aan beveiligingsprincipels. Deze machtigingen worden roltoewijzingen genoemd.

In de context van de sleutelkluis bepalen deze roltoewijzingen het toegangsniveau van een beveiligingsprincipaal tot het beheervlak (ook wel het besturingsvlak genoemd) van de sleutelkluis. Deze roltoewijzingen bieden niet rechtstreeks toegang tot de gegevensvlakgeheimen, maar bieden wel toegang tot het beheren van eigenschappen van de sleutelkluis. Een gebruiker of toepassing met  de rol Lezer mag bijvoorbeeld geen wijzigingen aanbrengen in de firewallinstellingen  van de sleutelkluis, terwijl een gebruiker of toepassing met de rol Inzender wijzigingen kan aanbrengen. Geen van beide rollen heeft directe toegang tot het uitvoeren van bewerkingen op geheimen, sleutels en certificaten, zoals het maken of ophalen van hun waarde totdat ze toegang krijgen tot het gegevensvlak van de sleutelkluis. Dit wordt behandeld in de volgende stap.

>[!IMPORTANT]
> Hoewel gebruikers met de rol Inzender of Eigenaar niet standaard toegang hebben tot het uitvoeren van bewerkingen op geheimen die zijn opgeslagen in de sleutelkluis, bieden de rollen Inzender en Eigenaar machtigingen voor het toevoegen of verwijderen van toegangsbeleid voor geheimen die zijn opgeslagen in key vault. Daarom kan een gebruiker met deze roltoewijzingen zichzelf toegang verlenen tot geheimen in de sleutelkluis. Daarom wordt het aanbevolen dat alleen beheerders toegang hebben tot de rollen Inzender of Eigenaar. Gebruikers en toepassingen die alleen geheimen uit de sleutelkluis hoeven op te halen, moeten de rol Lezer krijgen. **Meer informatie in de volgende sectie.**

>[!NOTE]
> Wanneer u een roltoewijzing toewijst aan een gebruiker op het niveau van de Azure Active Directory-tenant, wordt deze set machtigingen beperkt tot alle abonnementen, resourcegroepen en resources binnen het bereik van de toewijzing. Als u de principal met de minste bevoegdheden wilt volgen, kunt u deze roltoewijzing een gedetailleerder bereik geven. U kunt een gebruiker bijvoorbeeld de rol Lezer toewijzen op abonnementsniveau en de rol Eigenaar voor één sleutelkluis. Ga naar de IAM-instellingen (Identity Access Management) van een abonnement, resourcegroep of sleutelkluis om een roltoewijzing met een gedetailleerder bereik te maken.

* Voor meer informatie over de koppeling [Azure-rollen](../../role-based-access-control/built-in-roles.md)
* Koppeling voor meer informatie over het toewijzen of verwijderen van [roltoewijzingen](../../role-based-access-control/role-assignments-portal.md)

## <a name="configure-key-vault-access-policies-for-your-security-principal"></a>Toegangsbeleid voor key vault configureren voor uw beveiligingsprincipaal

Voordat u uw gebruikers en toepassingen toegang verleent tot Key Vault, is het belangrijk om inzicht te krijgen in de verschillende typen bewerkingen die op een sleutelkluis kunnen worden uitgevoerd. Er zijn twee belangrijke typen sleutelkluisbewerkingen: beheervlakbewerkingen (ook wel besturingsvlak genoemd) en bewerkingen voor gegevensvlak.

Deze tabel bevat verschillende voorbeelden van de verschillende bewerkingen die worden beheerd door het beheervlak versus het gegevensvlak. Bewerkingen die de eigenschappen van de sleutelkluis wijzigen, zijn bewerkingen op de beheervlak. Bewerkingen die de waarde wijzigen of ophalen van geheimen die zijn opgeslagen in de sleutelkluis zijn bewerkingen op het gegevensvlak.

|Bewerkingen op beheervlak (voorbeelden)|Gegevensvlakbewerkingen (voorbeelden)|
| --- | --- |
| Een Key Vault | Een sleutel, geheim, certificaat maken
| Verwijderen Key Vault | Een sleutel, geheim, certificaat verwijderen
| Roltoewijzingen Key Vault of verwijderen | Waarden van sleutels, geheimen, certificaten opsnnen en ophalen
| Toegangsbeleid voor Key Vault toevoegen of verwijderen | Back-up en herstel van sleutels, geheimen, certificaten
| Firewallinstellingen Key Vault wijzigen | Sleutels, geheimen, certificaten vernieuwen
| Herstelinstellingen Key Vault wijzigen | Zacht verwijderde sleutels, geheimen, certificaten leeg maken of herstellen
| Instellingen Key Vault diagnostische logboeken wijzigen

### <a name="management-plane-access--azure-active-directory-role-assignments"></a>Toegangsbeheervlak & Azure Active Directory roltoewijzingen

Azure Active Directory roltoewijzingen toegang verlenen om bewerkingen op de beheervlak uit te voeren op een sleutelkluis. Deze toegang wordt doorgaans verleend aan gebruikers, niet aan toepassingen. U kunt beperken welke beheervlakbewerkingen een gebruiker kan uitvoeren door de roltoewijzing van een gebruiker te wijzigen.

Als een gebruiker bijvoorbeeld de rol Key Vault lezer toewijst aan een gebruiker, kunnen ze de eigenschappen van uw sleutelkluis zien, zoals toegangsbeleid, maar kunnen ze geen wijzigingen aanbrengen. Als u een gebruiker toewijst, krijgt deze met de rol Eigenaar volledige toegang tot het wijzigen van de beheervlakinstellingen van de sleutelkluis.

Roltoewijzingen worden beheerd in de blade sleutelkluis Access Control (IAM). Als u wilt dat een bepaalde gebruiker toegang heeft als lezer of beheerder van meerdere key vault-resources, kunt u een roltoewijzing maken op het niveau van de kluis, resourcegroep of abonnement, en wordt de roltoewijzing toegevoegd aan alle resources binnen het bereik van de toewijzing.

Toegang tot de gegevensvlak of toegang om bewerkingen uit te voeren op sleutels, geheimen en certificaten die zijn opgeslagen in de sleutelkluis, kan op twee manieren worden toegevoegd.

### <a name="data-plane-access-option-1-classic-key-vault-access-policies"></a>Toegangsoptie voor gegevensvlak 1: klassiek Key Vault toegangsbeleid

Toegangsbeleid van Key Vault verleent gebruikers en toepassingen toegang tot het uitvoeren van gegevensvlakbewerkingen op een sleutelkluis.

> [!NOTE]
> Dit toegangsmodel is niet compatibel met Azure RBAC voor sleutelkluis (optie 2) die hieronder wordt beschreven. U moet er een kiezen. U kunt deze selectie maken wanneer u op het tabblad Toegangsbeleid van uw sleutelkluis klikt.

Klassieke toegangsbeleidsregels zijn gedetailleerd, wat betekent dat u de mogelijkheid van elke afzonderlijke gebruiker of toepassing om afzonderlijke bewerkingen binnen een sleutelkluis uit te voeren, kunt toestaan of weigeren. Enkele voorbeelden:

* Security Principal 1 kan een sleutelbewerking uitvoeren, maar mag geen geheim- of certificaatbewerkingen uitvoeren.
* Beveiligingsprincipaal 2 kan alle sleutels, geheimen en certificaten in een lijst en lezen, maar kan geen bewerkingen voor maken, verwijderen of vernieuwen uitvoeren.
* Security Principal 3 kan een back-up maken van alle geheimen en deze herstellen, maar kan de waarde van de geheimen zelf niet lezen.

Klassiek toegangsbeleid staat echter geen machtigingen per objectniveau toe en toegewezen machtigingen worden toegepast op het bereik van een afzonderlijke sleutelkluis. Als u bijvoorbeeld de toegangsbeleidsmachtiging 'Secret Get' verleent aan een beveiligingsprincipaal in een bepaalde sleutelkluis, heeft de beveiligingsprincipaal de mogelijkheid om alle geheimen binnen die specifieke sleutelkluis op te halen. Deze machtiging 'Geheim maken' wordt echter niet automatisch uitgebreid naar andere sleutelkluizen en moet expliciet worden toegewezen.

> [!IMPORTANT]
> Klassiek toegangsbeleid voor sleutelkluizen en Azure Active Directory roltoewijzingen zijn onafhankelijk van elkaar. Als u een beveiligingsprincipaal de rol 'Inzender' toewijst op abonnementsniveau, kan de beveiligingsprincipaal niet automatisch bewerkingen op het gegevensvlak uitvoeren op elke sleutelkluis binnen het bereik van het abonnement. De beveiligingsprincipaal moet nog steeds worden verleend of zichzelf toegangsbeleidmachtigingen verlenen om gegevensvlakbewerkingen uit te voeren.

### <a name="data-plane-access-option-2--azure-rbac-for-key-vault"></a>Toegang tot gegevensvlak optie 2: Azure RBAC voor Key Vault

Een nieuwe manier om toegang te verlenen tot de gegevensvlak van de sleutelkluis is via op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) voor key vault.

> [!NOTE]
> Dit toegangsmodel is niet compatibel met het klassieke toegangsbeleid van Key Vault dat hierboven wordt weergegeven. U moet er een kiezen. U kunt deze selectie maken wanneer u op het tabblad Toegangsbeleid van uw sleutelkluis klikt.

Key Vault roltoewijzingen zijn een set ingebouwde Azure-roltoewijzingen die algemene sets machtigingen omvatten die worden gebruikt voor toegang tot sleutels, geheimen en certificaten. Dit machtigingsmodel maakt ook aanvullende mogelijkheden mogelijk die niet beschikbaar zijn in het klassieke toegangsbeleidsmodel van de sleutelkluis.

* Azure RBAC-machtigingen kunnen op schaal worden beheerd doordat gebruikers deze rollen kunnen toewijzen op abonnements-, resourcegroep- of afzonderlijke sleutelkluisniveau. Een gebruiker heeft machtigingen voor de gegevensvlak voor alle sleutelkluizen binnen het bereik van de Azure RBAC-toewijzing. Hierdoor hoeft u geen afzonderlijke toegangsbeleidmachtigingen per gebruiker/toepassing per sleutelkluis toe te wijzen.

* Azure RBAC-machtigingen zijn compatibel met Privileged Identity Management of PIM. Hiermee kunt u Just-In-Time-toegangsbesturingselementen configureren voor bevoorrechte rollen, Key Vault beheerder. Dit is een best-security practice en volgt de principal van de minste bevoegdheden door permanente toegang tot uw sleutelkluizen te elimineren.

Zie de volgende documenten voor meer informatie over Azure RBAC voor Key Vault:

* Azure RBAC voor Key Vault [koppeling](./secure-your-key-vault.md#management-plane-and-azure-rbac)
* Koppeling naar Azure RBAC [Key Vault-rollen](../../role-based-access-control/built-in-roles.md#key-vault-administrator)

## <a name="configure-key-vault-firewall"></a>Een firewall Key Vault configureren

Standaard staat key vault toe dat verkeer van het openbare internet uw sleutelkluis kan verzenden via een openbaar eindpunt. Voor een extra beveiligingslaag kunt u de firewall configureren Azure Key Vault toegang tot het openbare eindpunt van de sleutelkluis te beperken.

Als u de sleutelkluisfirewall wilt inschakelen, klikt u op het tabblad Netwerken in de portal van de sleutelkluis en selecteert u het keuzerondje Toegang toestaan vanuit: Privé-eindpunt en Geselecteerde netwerken. Als u ervoor kiest om de firewall van de sleutelkluis in teschakelen, zijn dit de manieren waarop u verkeer via de firewall van de sleutelkluis kunt toestaan.

* Voeg IPv4-adressen toe aan de lijst met toegestane adressen voor de sleutelkluisfirewall. Deze optie werkt het beste voor toepassingen met statische IP-adressen.

* Voeg een virtueel netwerk toe aan de firewall van de sleutelkluis. Deze optie werkt het beste voor Azure-resources met dynamische IP-adressen, zoals Virtual Machines. U kunt Azure-resources toevoegen aan een virtueel netwerk en het virtuele netwerk toevoegen aan de lijst met toegestane firewalls voor de sleutelkluis. Deze optie maakt gebruik van een service-eindpunt dat een privé-IP-adres in het virtuele netwerk is. Dit biedt een extra beveiligingslaag, zodat er geen verkeer tussen key vault en uw virtuele netwerk wordt gerouteerd via het openbare internet. Zie de volgende documentatie voor meer informatie over service-eindpunten. [Link](./network-security.md)

* Voeg een private link-verbinding toe aan de sleutelkluis. Met deze optie wordt uw virtuele netwerk rechtstreeks verbonden met een bepaald exemplaar van de sleutelkluis, waardoor uw sleutelkluis in uw virtuele netwerk wordt gebruikt. Zie de volgende koppeling voor meer informatie over het configureren van een privé-eindpuntverbinding met de [sleutelkluis](./private-link-service.md)

## <a name="test-your-service-principals-ability-to-access-key-vault"></a>De mogelijkheid van uw service-principal voor toegang tot de sleutelkluis testen

Nadat u alle bovenstaande stappen hebt gevolgd, kunt u geheimen instellen en ophalen uit uw sleutelkluis.

### <a name="authentication-process-for-users-examples"></a>Verificatieproces voor gebruikers (voorbeelden)

* Gebruikers kunnen zich aanmelden bij de Azure Portal key vault te gebruiken. [Key Vault portal Quickstart](./quick-create-portal.md)

* De gebruiker kan Azure CLI gebruiken om key vault te gebruiken. [Key Vault Azure CLI-snelstart](./quick-create-cli.md)

* De gebruiker kan Azure PowerShell sleutelkluis gebruiken. [Key Vault Azure PowerShell Quickstart](./quick-create-powershell.md)

### <a name="azure-active-directory-authentication-process-for-applications-or-services-examples"></a>Azure Active Directory verificatieproces voor toepassingen of services (voorbeelden)

* Een toepassing biedt een clientgeheim en client-id in een functie om een Azure Active Directory token op te halen. 

* Een toepassing biedt een certificaat voor het verkrijgen van een Azure Active Directory token. 

* Een Azure-resource maakt gebruik van MSI-verificatie om een Azure Active Directory op te halen. 

* Meer informatie over [](../../active-directory/managed-identities-azure-resources/overview.md) de MSI-verificatiekoppeling

### <a name="authentication-process-for-application-python-example"></a>Verificatieproces voor toepassing (Python-voorbeeld)

Gebruik het volgende codevoorbeeld om te testen of uw toepassing een geheim kan ophalen uit uw sleutelkluis met behulp van de service-principal die u hebt geconfigureerd.

>[!NOTE]
>Dit voorbeeld is alleen bedoeld voor demonstratie- en testdoeleinden. **GEBRUIK GEEN VERIFICATIE VAN CLIENTGEHEIMEN IN PRODUCTIE** Dit is geen veilige ontwerp practice. Gebruik clientcertificaat of MSI-verificatie als een best practice.

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

Zie het volgende document voor meer informatie over sleutelkluisverificatie. [Key Vault-verificatie](./authentication.md)
