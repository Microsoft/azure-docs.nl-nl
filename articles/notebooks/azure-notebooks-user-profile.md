---
title: Gebruikers profiel en-ID voor gebruik met Azure Notebooks preview
description: Uw gebruikers profiel en gebruikers-ID maken en beheren met Azure Notebooks, dat onderdeel wordt van de URL van gedeelde notitie blokken.
ms.topic: conceptual
ms.date: 02/25/2019
ms.openlocfilehash: 30d70365fcc0c72df01b4dc059b6e0f4cc607bba
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109501"
---
# <a name="your-profile-and-user-id-for-azure-notebooks-preview"></a>Uw profiel en gebruikers-ID voor Azure Notebooks preview

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Binnen de krachtige, samenwerkings ruimte van Azure Notebooks presenteert uw gebruikers profiel uw open bare installatie kopie voor anderen:

[![Een Azure Notebooks profiel pagina](media/accounts/profile-page.png)](media/accounts/profile-page.png#lightbox)

Uw gebruikers-ID maakt deel uit van de Url's die u gebruikt om projecten en notitie blokken te delen. In de volgende lijst worden de verschillende URL-patronen beschreven:

- `https://notebooks.azure.com/<user_id>`: Uw profiel pagina.
- `https://notebooks.azure.com/<user_id>/projects`: Uw projecten. U ziet alle projecten; andere gebruikers zien alleen uw open bare projecten.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>`: Project bestanden.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/clones`: Klonen van een bepaalde projecten.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/html/<notebook>.ipynb`: De HTML-preview van een specifiek notitie blok of bestand.

## <a name="your-user-id"></a>Uw gebruikers-id

Als u zich voor de eerste keer aanmeldt bij Azure Notebooks, wordt aan uw account automatisch een tijdelijke gebruikers-ID toegewezen, zoals ' veri-idr3ca '. Zolang u een gebruikers-ID hebt die begint met ' veri-', wordt u door Azure Notebooks gevraagd deze te wijzigen wanneer u zich aanmeldt:

![Vragen om een gebruikers-ID te maken wanneer u zich aanmeldt bij Azure Notebooks](media/accounts/create-user-id.png)

De opdracht **gebruikers-id configureren** wordt ook weer gegeven naast de tijdelijke gebruikers naam:

![De opdracht gebruikers-ID configureren die wordt weer gegeven wanneer u een tijdelijke ID gebruikt](media/accounts/configure-user-id-command.png)

U kunt ook uw gebruikers-ID op elk gewenst moment wijzigen op de profiel pagina.

Een gebruikers-ID moet bestaan uit vier tot zestien letters, cijfers en afbreek streepjes. Er zijn geen andere tekens toegestaan en de gebruikers-ID mag niet beginnen of eindigen met een koppel teken of meerdere afbreek streepjes gebruiken in een rij. Omdat gebruikers-Id's uniek zijn voor alle Azure Notebooks accounts, ziet u mogelijk het bericht ' de gebruikers-ID is al in gebruik '. (Het bericht wordt ook weer gegeven als u een micro soft-handels merk wilt gebruiken als een gebruikers-ID). In deze gevallen kiest u een andere gebruikers-ID.

> [!Important]
> Als u uw ID wijzigt, worden de Url's die u mogelijk hebt gedeeld met uw vorige ID, ongeldig gemaakt. U kunt uw ID weer wijzigen in uw vorige ID om de koppelingen opnieuw te valideren. Het is echter mogelijk dat een andere gebruiker een niet-gebruikte ID in de tussen tijd kan claimen.

## <a name="your-profile"></a>Uw profiel

Uw profiel bestaat uit openbaar zicht bare informatie op de URL `https://notebooks.azure.com/<user_id>` . Uw profiel pagina bevat ook uw recent gebruikte projecten en gemarkeerd-projecten.

Als u uw profiel wilt bewerken, gebruikt u de opdracht **Profiel gegevens bewerken** op de profiel pagina. De secties van uw profiel zijn als volgt:

| Sectie | Inhoud |
| --- | --- |
| Profiel foto | Een afbeelding die wordt weer gegeven op de profiel pagina. |
| Accountgegevens | Uw weergave naam, gebruikers-ID en open bare e-mail account. Het e-mail account biedt andere gebruikers een gemiddelde om contact met u op te nemen en kan afwijken van het [account](azure-notebooks-user-account.md) dat u gebruikt om u aan te melden Azure notebooks zichzelf. |
| Profiel gegevens | Uw locatie, bedrijf, functie titel, website en een korte beschrijving van uzelf. |
| Sociale profielen | Uw GitHub-, Twitter-en Facebook-Id's als u ze wilt delen. |
| Privacyinstellingen | Biedt twee opdrachten:<ul><li>**Mijn profiel exporteren**: maakt en downloadt een *zip* -bestand met alle informatie die Azure notebooks opgeslagen in uw profiel, inclusief uw foto, profiel gegevens en beveiligings Logboeken.</li><li>**Mijn account verwijderen**: alle persoonlijke gegevens die zijn opgeslagen in azure notebooks, worden permanent verwijderd.</li></ul> |
| Site functies inschakelen | Hiermee kunt u aspecten van het gedrag van Azure Notebooks beheren:<ul><li>**Unified frontend voor notebooks**: Hiermee kunt u sneller opstarten van notebooks en betere persistentie.</li><li>Wordt **standaard uitgevoerd in jjupyterlab**: Azure notebooks biedt standaard een eenvoudige gebruikers interface die geschikt is voor de meeste gebruikers. Jjupyterlab biedt een rijkere maar complexere interface voor ervaren gebruikers.</li><li>**VNext-website**: Hiermee wordt de moderne webindeling ingeschakeld die in deze documentatie wordt weer gegeven.</li></ul> |

## <a name="next-steps"></a>Volgende stappen  

> [!div class="nextstepaction"]
> [Snelstartgids: een Jupyter Notebook-project exporteren](quickstart-export-jupyter-notebook-project.md)
