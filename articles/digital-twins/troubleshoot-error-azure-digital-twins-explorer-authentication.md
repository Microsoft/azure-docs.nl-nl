---
title: Azure Digital Twins Explorer verificatiefout
description: Oorzaken en oplossingen voor 'Verificatie mislukt'. in Azure Digital Twins Explorer.
ms.service: digital-twins
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.date: 4/8/2021
ms.openlocfilehash: 1f8373130fbead2204dd0ac2515595d68dd3b2e8
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107495046"
---
# <a name="authentication-failed"></a>Verificatie is mislukt

In dit artikel worden de oorzaken en oplossingsstappen beschreven voor het ontvangen van de fout 'Verificatie is mislukt' tijdens het uitvoeren van [Azure Digital Twins Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) voorbeeld op uw lokale computer. 

## <a name="symptoms"></a>Symptomen

Bij het instellen en uitvoeren van Azure Digital Twins Explorer toepassing, wordt het volgende foutbericht weergegeven bij pogingen om te verifiëren met de app:

:::image type="content" source="media/troubleshoot-error-azure-digital-twins-explorer-authentication/authentication-error.png" alt-text="Schermopname van een foutbericht in Azure Digital Twins explorer met de volgende tekst: Verificatie is mislukt. Als u de app lokaal gebruikt, moet u ervoor zorgen dat u bent aangemeld bij Azure op uw hostmachine of door 'az login' uit te voeren in een opdrachtprompt, door u aan te melden bij Visual Studio of VS Code of door omgevingsvariabelen in te stellen. Als u meer informatie nodig hebt, raadpleegt u het leesmij-document of bekijkt u DefaultAzureCredential in de documentatie van Azure.Identity. Als u adt-explorer gebruikt die wordt gehost in de cloud, moet u ervoor zorgen dat er een door het systeem toegewezen beheerde identiteit is ingesteld voor uw Azure-functie die als host wordt gehost. Zie het leesmij-artikel voor meer informatie.":::

## <a name="causes"></a>Oorzaken

### <a name="cause-1"></a>Oorzaak #1

De Azure Digital Twins Explorer maakt gebruik [van DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) (onderdeel van de bibliotheek), waarmee wordt gezocht naar referenties `Azure.Identity` in uw lokale omgeving.

Als de fouttekst wordt weergegeven, kan deze fout optreden als u geen lokale referenties hebt opgegeven om `DefaultAzureCredential` op te halen.

Voor meer informatie over het gebruik van lokale referenties met Azure Digital Twins Explorer, zie de sectie Lokale [*Azure-referenties*](quickstart-adt-explorer.md#set-up-local-azure-credentials) instellen van de Azure Digital Twins *Quickstart: Een voorbeeldscenario verkennen.*

### <a name="cause-2"></a>Oorzaak #2

Deze fout kan ook optreden als uw Azure-account niet over de vereiste Machtigingen voor op rollen gebaseerd toegangsbeheer (Azure RBAC) van Azure is ingesteld op uw Azure Digital Twins exemplaar. Als u toegang wilt krijgen tot gegevens in uw exemplaar, moet u respectievelijk de **rol Azure Digital Twins Data Reader of** Azure Digital Twins Data **Owner** hebben voor het exemplaar dat u wilt lezen of beheren. 

Zie Concepten: Beveiliging voor Azure Digital Twins-oplossingen voor meer informatie over beveiliging en rollen Azure Digital Twins [*in Azure Digital Twins.*](concepts-security.md)

## <a name="solutions"></a>Oplossingen

### <a name="solution-1"></a>Oplossingsoplossing #1

Zorg er eerst voor dat u de benodigde referenties hebt opgegeven voor de toepassing.

#### <a name="provide-local-credentials"></a>Lokale referenties verstrekken

`DefaultAzureCredential` wordt geverifieerd bij de service met behulp van de gegevens van een lokale Azure-aanmelding. U kunt uw Azure-referenties verstrekken door u aan te melden bij uw Azure-account in een lokaal [Azure CLI-venster](/cli/azure/install-azure-cli) of in Visual Studio of Visual Studio Code.

U kunt de referentietypen weergeven die accepteren, evenals de volgorde waarin ze worden geprobeerd, in de Azure Identity-documentatie voor `DefaultAzureCredential` [DefaultAzureCredential.](/dotnet/api/overview/azure/identity-readme#defaultazurecredential)

Als u al lokaal bent aangemeld bij het juiste Azure-account en het probleem niet is opgelost, gaat u verder met de volgende oplossing.

### <a name="solution-2"></a>Oplossingsoplossing #2

Controleer of uw Azure-gebruiker de **rol Azure Digital Twins Data Reader** heeft op het Azure Digital Twins-exemplaar als u alleen de gegevens probeert te **lezen, of** de rol Azure Digital Twins Gegevenseigenaar voor het exemplaar als u de gegevens probeert te beheren.

Houd er rekening mee dat deze rol verschilt van...
* de voormalige naam voor deze rol tijdens de preview, *Azure Digital Twins Eigenaar (preview) (de* rol is hetzelfde, maar de naam is gewijzigd)
* de *rol Van* eigenaar voor het hele Azure-abonnement. *Azure Digital Twins eigenaar* van gegevens is een rol binnen Azure Digital Twins en is beperkt tot deze afzonderlijke Azure Digital Twins exemplaar.
* de *rol* Eigenaar in Azure Digital Twins. Dit zijn twee verschillende Azure Digital Twins en *Azure Digital Twins* is de rol die moet worden gebruikt voor beheer.

 Als u deze rol niet hebt, stelt u deze in om het probleem op te lossen.

#### <a name="check-current-setup"></a>Huidige installatie controleren

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

#### <a name="fix-issues"></a>Problemen oplossen 

Als u deze roltoewijzing niet hebt, moet iemand met de rol Eigenaar in uw **Azure-abonnement** de volgende opdracht uitvoeren om uw Azure-gebruiker de juiste rol te geven op het **Azure Digital Twins-exemplaar**. 

Als u een eigenaar van het abonnement bent, kunt u deze opdracht zelf uitvoeren. Als u dat niet bent, neem dan contact op met een eigenaar om deze opdracht namens u uit te voeren. De naam van de rol is **Azure Digital Twins gegevenseigenaar voor** bewerkingstoegang of **Azure Digital Twins gegevenslezer voor** leestoegang.

```azurecli-interactive
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<your-Azure-AD-email>" --role "<role-name>"
```

Zie voor meer informatie over deze rolvereiste en het toewijzingsproces de sectie [ *Toegangsmachtigingen*](how-to-set-up-instance-CLI.md#set-up-user-access-permissions) van uw gebruiker instellen van *Procedure: Een* exemplaar en verificatie instellen (CLI of portal).

## <a name="next-steps"></a>Volgende stappen

Lees de installatiestappen voor het maken en authenticeren van een Azure Digital Twins-exemplaar:
* [*How-to: Set up an instance and authentication (CLI) (Een exemplaar en verificatie instellen (CLI)*](how-to-set-up-instance-cli.md)

Meer informatie over beveiliging en machtigingen voor Azure Digital Twins:
* [*Concepten: Beveiliging voor Azure Digital Twins oplossingen*](concepts-security.md)
