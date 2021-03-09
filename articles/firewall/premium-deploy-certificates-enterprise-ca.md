---
title: CA-certificaten voor ondernemingen implementeren en configureren voor Azure Firewall Premium preview
description: Meer informatie over het implementeren en configureren van ondernemings-CA-certificaten voor Azure Firewall Premium preview.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 03/09/2021
ms.author: victorh
ms.openlocfilehash: fba95214a6bbb0482166eab8f77f30911986fbb7
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102525482"
---
# <a name="deploy-and-configure-enterprise-ca-certificates-for-azure-firewall-preview"></a>CA-certificaten voor ondernemingen implementeren en configureren voor Azure Firewall preview

> [!IMPORTANT]
> Azure Firewall Premium is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.


Azure Firewall Premium Preview bevat een TLS-inspectie functie waarvoor een verificatie keten voor certificaten is vereist. Voor productie-implementaties moet u een Enter prise PKI gebruiken om de certificaten te genereren die u gebruikt met Azure Firewall Premium. Gebruik dit artikel voor het maken en beheren van een tussenliggend CA-certificaat voor Azure Firewall Premium preview.

Zie [Azure Firewall Premium preview-certificaten](premium-certificates.md)voor meer informatie over de certificaten die worden gebruikt door Azure Firewall Premium preview.

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

Als u een CA voor ondernemingen wilt gebruiken om een certificaat te genereren voor gebruik met Azure Firewall Premium preview, hebt u de volgende resources nodig: 

- een Active Directory-forest 
- een Active Directory certificerings Services-basis-CA met Internet registratie ingeschakeld 
- een Azure Firewall Premium met een firewall beleid voor de Premium-laag 
- een Azure Key Vault 
- een beheerde identiteit met lees machtigingen voor **certificaten en geheimen** die zijn gedefinieerd in het beleid voor Key Vault toegang 

## <a name="request-and-export-a-certificate"></a>Een certificaat aanvragen en exporteren

1. Open de site voor Internet registratie op de basis-CA, meestal `https://<servername>/certsrv` en selecteer **een certificaat aanvragen**.
1. Selecteer **Geavanceerde certificaat aanvraag**.
1. Selecteer **maken en verzenden van een aanvraag naar deze certificerings instantie**.
1. Vul het formulier in met behulp van de sjabloon voor de onderliggende certificerings instantie, zoals wordt weer gegeven:
1. Dien de aanvraag in en installeer het certificaat.
1. Als deze aanvraag wordt gedaan vanaf een Windows-Server met Internet Explorer, opent u **Internet opties**.
1. Navigeer naar het tabblad **inhoud** en selecteer **certificaten**.
1. Selecteer het certificaat dat zojuist is uitgegeven en selecteer vervolgens **exporteren**.
1. Selecteer **volgende** om de wizard te starten. Selecteer **Ja, de persoonlijke sleutel exporteren** en selecteer **volgende**.
1. de indeling. pfx-bestand is standaard geselecteerd. Schakel **indien mogelijk alle certificaten in het certificeringspad in**. Als u de hele certificaat keten exporteert, mislukt het import proces naar Azure Firewall.
1. Wijs een wacht woord toe en bevestig dit om de sleutel te beveiligen en selecteer vervolgens **volgende**.
1. Kies een bestands naam en een export locatie en selecteer vervolgens **volgende**.
1. Selecteer **volt ooien** en verplaats het geëxporteerde certificaat naar een veilige locatie.

## <a name="add-the-certificate-to-a-firewall-policy"></a>Het certificaat toevoegen aan een firewall beleid

1. Navigeer in het Azure Portal naar de pagina certificaten van uw Key Vault en selecteer **genereren/importeren**.
1. Selecteer **importeren** als de methode voor het maken van het certificaat, selecteer het geëxporteerde pfx-bestand, voer het wacht woord in en selecteer vervolgens **maken**.
1. Ga naar de pagina **TLS-inspectie (voor beeld)** van uw firewall beleid en selecteer uw beheerde identiteit, Key Vault en certificaat. 
1. Selecteer **Opslaan**.
   :::image type="content" source="media/premium-deploy-certificates-enterprise-ca/tls-inspection.png" alt-text="TLS-inspectie":::

## <a name="validate-tls-inspection"></a>TLS-inspectie valideren

1. Maak een toepassings regel met behulp van TLS-inspectie naar de doel-URL of de FQDN van uw keuze.  Bijvoorbeeld: `*bing.com`.
1. Ga vanaf een computer die lid is van een domein binnen het bron bereik van de regel naar uw bestemming en selecteer het vergrendelings symbool naast de adres balk in uw browser. In het certificaat moet worden weer gegeven dat het is uitgegeven door uw CA voor ondernemingen in plaats van een open bare certificerings instantie.
1. Het certificaat weer geven om meer details weer te geven, met inbegrip van het certificaatpad.
   :::image type="content" source="media/premium-deploy-certificates-enterprise-ca/certificate-details.png" alt-text="certificaat Details":::
1. Voer in Log Analytics de volgende KQL-query uit om alle aanvragen te retour neren die zijn onderworpen aan TLS-inspectie:
   ```
   AzureDiagnostics 
   | where ResourceType == "AZUREFIREWALLS" 
   | where Category == "AzureFirewallApplicationRule" 
   | where msg_s contains "Url:" 
   | sort by TimeGenerated desc
   ```
   In het resultaat wordt de volledige URL van het geïnspecteerde verkeer weer gegeven: :::image type="content" source="media/premium-deploy-certificates-enterprise-ca/kql-query.png" alt-text="KQL-query":::

## <a name="next-steps"></a>Volgende stappen

[Azure Firewall Premium preview in het Azure Portal](premium-portal.md)
