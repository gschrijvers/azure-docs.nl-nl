---
title: 'Azure Red Hat open SHIFT met openshift 4: Configureer Azure Active Directory-verificatie met behulp van de Azure Portal en de open Shift-webconsole'
description: Meer informatie over het configureren van Azure Active Directory-verificatie voor een open ploeg cluster van Azure Red Hat met openshift 4 met behulp van de Azure Portal en de webconsole open Shift
ms.service: container-service
ms.topic: article
ms.date: 03/12/2020
author: sabbour
ms.author: asabbour
keywords: Aro, open Shift, AZ Aro, Red Hat, cli
ms.custom: mvc
ms.openlocfilehash: 6b6248aac35c22b9ffd2cd95df41e84986356259
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82205311"
---
# <a name="configure-azure-active-directory-authentication-for-an-azure-red-hat-openshift-4-cluster-portal"></a>Azure Active Directory authenticatie configureren voor een Azure Red Hat open Shift 4-cluster (Portal)

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor deze zelf studie gebruikmaken van de Azure CLI-versie 2.0.75 of hoger. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="before-you-begin"></a>Voordat u begint

Maak de **OAuth call back-URL** van het cluster en noteer deze. Zorg ervoor dat u **Aro-RG** vervangt door de naam van de resource groep en het **Aro-cluster** met de naam van uw cluster.

> [!NOTE]
> De `AAD` sectie in de OAuth call back-URL moet overeenkomen met de naam van de OAuth ID-provider die u later gaat instellen.

```azurecli-interactive
domain=$(az aro show -g aro-rg -n aro-cluster --query clusterProfile.domain -o tsv)
location=$(az aro show -g aro-rg -n aro-cluster --query location -o tsv)
echo "OAuth callback URL: https://oauth-openshift.apps.$domain.$location.aroapp.io/oauth2callback/AAD"
```

## <a name="create-an-azure-active-directory-application-for-authentication"></a>Een Azure Active Directory-toepassing maken voor verificatie

Meld u aan bij de Azure Portal en navigeer naar [app-registraties Blade](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)en klik vervolgens op **nieuwe registratie** om een nieuwe toepassing te maken.

Geef een naam op voor de toepassing, bijvoorbeeld **Aro-azuread-auth**, en vul de **omleidings-URI** in met de waarde van de OAUTH call back-URL die u eerder hebt opgehaald.

![Nieuwe toepassing registreren](media/aro4-ad-registerapp.png)

Navigeer naar **certificaten & geheimen** en klik op **Nieuw client geheim** en vul de details in. Noteer de sleutel waarde, omdat u deze in een later stadium gebruikt. Het is niet meer mogelijk om het opnieuw op te halen.

![Een geheim maken](media/aro4-ad-clientsecret.png)

Navigeer naar het **overzicht** en noteer de id van de **toepassing (client) ID** en de **Directory (Tenant)**. U hebt deze in een later stadium nodig.

![Toepassings-Id's (client) en Directory (Tenant) ophalen](media/aro4-ad-ids.png)

## <a name="configure-optional-claims"></a>Optionele claims configureren

Ontwikkel aars van toepassingen kunnen [optionele claims](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims) in hun Azure AD-toepassingen gebruiken om op te geven welke claims ze willen in tokens die naar hun toepassing worden verzonden.

U kunt optionele claims gebruiken voor het volgende:

* Selecteer extra claims die moeten worden toegevoegd aan de tokens voor uw toepassing.
* Wijzig het gedrag van bepaalde claims die Azure AD retourneert in tokens.
* Aangepaste claims toevoegen en openen voor uw toepassing.

We configureren open Shift om de `email` claim te gebruiken en terug te `upn` vallen op om de voorkeurs gebruikersnaam in te stellen `upn` door het toevoegen van het id-token dat wordt geretourneerd door Azure Active Directory.

Navigeer naar **token configuratie (preview)** en klik op **optionele claim toevoegen**. Selecteer **id** en controleer vervolgens de **e-mail-** en **UPN** -claims.

![Een geheim maken](media/aro4-ad-tokens.png)

## <a name="assign-users-and-groups-to-the-cluster-optional"></a>Gebruikers en groepen toewijzen aan het cluster (optioneel)

Toepassingen die zijn geregistreerd in een Azure Active Directory-Tenant (Azure AD), zijn standaard beschikbaar voor alle gebruikers van de Tenant die zijn geverifieerd. Met Azure AD kunnen Tenant beheerders en ontwikkel aars een app beperken tot een specifieke set gebruikers of beveiligings groepen in de Tenant.

Volg de instructies in de Azure Active Directory-documentatie om [gebruikers en groepen toe te wijzen aan de app](https://docs.microsoft.com/azure/active-directory/develop/howto-restrict-your-app-to-a-set-of-users#app-registration).

## <a name="configure-openshift-openid-authentication"></a>Open Shift OpenID Connect-verificatie configureren

De `kubeadmin` referenties ophalen. Voer de volgende opdracht uit om het wacht woord voor `kubeadmin` de gebruiker te zoeken.

```azurecli-interactive
az aro list-credentials \
  --name aro-cluster \
  --resource-group aro-rg
```

In de volgende voorbeeld uitvoer ziet u dat het wacht `kubeadminPassword`woord in wordt weer gegeven.

```json
{
  "kubeadminPassword": "<generated password>",
  "kubeadminUsername": "kubeadmin"
}
```

U kunt de URL van de cluster console vinden door de volgende opdracht uit te voeren, die er als volgt uitziet:`https://console-openshift-console.apps.<random>.<region>.aroapp.io/`

```azurecli-interactive
 az aro show \
    --name aro-cluster \
    --resource-group aro-rg \
    --query "consoleProfile.url" -o tsv
```

Start de console-URL in een browser en meld u `kubeadmin` aan met de referenties.

Navigeer naar **beheer**, klik op **cluster instellingen**en selecteer vervolgens het tabblad **globale configuratie** . Schuif naar Select **OAuth**.

Schuif omlaag om **toevoegen** onder **id-providers** te selecteren en selecteer **OpenID Connect Connect**.
![Selecteer OpenID Connect Connect in de vervolg keuzelijst ID-providers](media/aro4-oauth-idpdrop.png)

Vul de naam in als **Aad**, de **client-id** als de **toepassings-id** en het **client geheim**. De **URL** van de uitgever heeft de volgende indeling `https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`:. Vervang de tijdelijke aanduiding door de Tenant-ID die u eerder hebt opgehaald.

![OAuth-details invullen](media/aro4-oauth-idp-1.png)

Schuif omlaag naar de sectie **claims** en werk de **Voorkeurs gebruikersnaam** bij om de waarde van de **UPN** -claim te gebruiken.

![Claim details invullen](media/aro4-oauth-idp-2.png)

## <a name="verify-login-through-azure-active-directory"></a>Aanmelden via Azure Active Directory verifiëren

Als u nu de open Shift-webconsole afmeldt en zich opnieuw probeert aan te melden, wordt een nieuwe optie weer gegeven om u aan te melden bij **Aad**. Mogelijk moet u enkele minuten wachten.

![Aanmeldings scherm met Azure Active Directory optie](media/aro4-login-2.png)
