---
title: Bekende problemen-Azure Digital Apparaatdubbels
description: Hulp krijgen bij het herkennen en beperken van bekende problemen met Azure Digital Apparaatdubbels.
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.service: digital-twins
ms.date: 07/14/2020
ms.custom: contperf-fy21q3
ms.openlocfilehash: a9735e355244d51464c66c10e02f97f03d2e67cd
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97673462"
---
# <a name="known-issues-in-azure-digital-twins"></a>Bekende problemen in azure Digital Apparaatdubbels

Dit artikel bevat informatie over bekende problemen met Azure Digital Apparaatdubbels.

## <a name="400-client-error-bad-request-in-cloud-shell"></a>"400-client fout: ongeldige aanvraag" in Cloud Shell

**Beschrijving van probleem:** Opdrachten in Cloud Shell op *https://shell.azure.com* een regel matig uitvoeren mislukt met de fout ' 400-client fout: ongeldige aanvraag voor URL: http://localhost:50342/oauth2/token ', gevolgd door de volledige Stack tracering.

| Is dit van invloed op mij? | Oorzaak | Oplossing |
| --- | --- | --- |
| In &nbsp; Azure &nbsp; Digital &nbsp; apparaatdubbels heeft dit invloed op de volgende opdracht groepen:<br><br>`az dt route`<br><br>`az dt model`<br><br>`az dt twin` | Dit is het resultaat van een bekend probleem in Cloud Shell: het [*verkrijgen van tokens van Cloud shell af en toe mislukt met 400-client fout: ongeldige aanvraag*](https://github.com/Azure/azure-cli/issues/11749).<br><br>Dit geeft een probleem met het Azure Digital Apparaatdubbels instance-verificatie tokens en de standaard verificatie op basis van [beheerde id's](../active-directory/managed-identities-azure-resources/overview.md) van de Cloud shell. <br><br>Dit heeft geen invloed op de Azure Digital Apparaatdubbels-opdrachten van de `az dt` `az dt endpoint` -of-opdracht groepen, omdat ze een ander type verificatie token gebruiken (op basis van Azure Resource Manager). Dit heeft geen probleem met de beheerde identiteits verificatie van de Cloud shell. | Een manier om dit probleem op te lossen, is door de `az login` opdracht opnieuw uit te voeren in Cloud shell en volgende aanmeldings stappen te volt ooien. Hiermee schakelt u de sessie uit van beheerde identiteits verificatie, waardoor het hoofd probleem wordt voor komen. Daarna kunt u de opdracht opnieuw uitvoeren.<br><br>U kunt ook het deel venster Cloud Shell in de Azure Portal openen en uw Cloud Shell werk van daaruit volt ooien.<br>:::image type="content" source="media/troubleshoot-known-issues/portal-launch-icon.png" alt-text="Afbeelding van het pictogram Cloud Shell op de Azure Portal pictogram balk" lightbox="media/troubleshoot-known-issues/portal-launch-icon.png":::<br><br>Ten slotte is het mogelijk om [de Azure cli](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) op uw computer te installeren, zodat u Azure cli-opdrachten lokaal kunt uitvoeren. Dit probleem treedt niet op bij de lokale CLI. |


## <a name="missing-role-assignment-after-scripted-setup"></a>Roltoewijzing ontbreekt na installatie van script

**Beschrijving van probleem:** Sommige gebruikers kunnen problemen ondervinden met het toewijzings deel van de functie [*: een exemplaar en verificatie instellen (script)*](how-to-set-up-instance-scripted.md). Het script geeft geen fout aan, maar de rol van de *Azure Digital apparaatdubbels-gegevens eigenaar* is niet aan de gebruiker toegewezen. dit probleem heeft gevolgen voor de mogelijkheid om nog andere resources te maken.

[!INCLUDE [digital-twins-role-rename-note.md](../../includes/digital-twins-role-rename-note.md)]

| Is dit van invloed op mij? | Oorzaak | Oplossing |
| --- | --- | --- |
| Als u wilt bepalen of uw roltoewijzing is ingesteld nadat het script is uitgevoerd, volgt u de instructies in de sectie toewijzing van gebruikersrol [*controleren*](how-to-set-up-instance-scripted.md#verify-user-role-assignment) van het artikel Setup. Als uw gebruiker niet met deze rol wordt weer gegeven, is dit van invloed op het probleem. | Voor gebruikers die zijn aangemeld met een persoonlijk [Microsoft-account (MSA)](https://account.microsoft.com/account), is de principal-id van uw gebruiker die u aanduidt in opdrachten zoals deze kan verschillen van de e-mail berichten van uw gebruiker, waardoor het script moeilijker kan worden gedetecteerd en gebruikt om de functie correct toe te wijzen. | Als u dit wilt oplossen, kunt u de roltoewijzing hand matig instellen met behulp van de [cli-instructies](how-to-set-up-instance-cli.md#set-up-user-access-permissions) of [Azure Portal instructies](how-to-set-up-instance-portal.md#set-up-user-access-permissions). |

## <a name="issue-with-interactive-browser-authentication"></a>Probleem met interactieve browser verificatie

**Beschrijving van probleem:** Wanneer u verificatie code in uw Azure Digital Apparaatdubbels-toepassingen schrijft met versie **1.2.0** van de **[Azure. Identity](/dotnet/api/azure.identity?view=azure-dotnet&preserve-view=true) -bibliotheek**, kunnen er problemen optreden met de [InteractiveBrowserCredential](/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet&preserve-view=true) -methode. Dit geeft als fout bericht ' Azure. Identity. AuthenticationFailedException ' bij het verifiëren van de verificatie in een browser venster. Het browser venster kan niet volledig worden gestart, of lijkt om de gebruiker te verifiëren, terwijl de client toepassing nog steeds mislukt met de fout.

| Is dit van invloed op mij? | Oorzaak | Oplossing |
| --- | --- | --- |
| De &nbsp; betrokken &nbsp; methode &nbsp; wordt &nbsp; gebruikt &nbsp; in &nbsp; de &nbsp; volgende artikelen:<br><br>[*Zelfstudie: Een client-app coderen*](tutorial-code.md)<br><br>[*Instructies: app-verificatie code schrijven*](how-to-authenticate-client.md)<br><br>[*Instructies: de Azure Digital Apparaatdubbels-Api's en Sdk's gebruiken*](how-to-use-apis-sdks.md) | Sommige gebruikers hebben dit probleem gehad met versie **1.2.0** van de `Azure.Identity` bibliotheek. | Werk uw toepassingen bij voor het gebruik van de [meest recente versie](https://www.nuget.org/packages/Azure.Identity) van om dit probleem op te lossen `Azure.Identity` . Nadat de bibliotheek versie is bijgewerkt, moet de browser naar verwachting laden en verifiëren. |

## <a name="next-steps"></a>Volgende stappen

Lees meer over beveiliging en machtigingen op Azure Digital Apparaatdubbels:
* [*Concepten: beveiliging voor Azure Digital Apparaatdubbels Solutions*](concepts-security.md)