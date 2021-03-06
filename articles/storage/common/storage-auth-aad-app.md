---
title: Een token van Azure AD verkrijgen voor het machtigen van aanvragen van een client toepassing
titleSuffix: Azure Storage
description: Gebruik Azure Active Directory om te verifiëren vanuit een client toepassing, verschaf een OAuth 2,0-token en machtig aanvragen voor Azure Blob Storage en Queue Storage.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.subservice: common
ms.custom: has-adal-ref
ms.openlocfilehash: 0cda75469edaa183ed6553a431b9ad13b611db7d
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/12/2020
ms.locfileid: "83201074"
---
# <a name="acquire-a-token-from-azure-ad-for-authorizing-requests-from-a-client-application"></a>Een token van Azure AD verkrijgen voor het machtigen van aanvragen van een client toepassing

Een belang rijk voor deel van het gebruik van Azure Active Directory (Azure AD) met Azure Blob Storage of de wachtrij opslag is dat uw referenties niet meer in uw code hoeven te worden opgeslagen. In plaats daarvan kunt u een OAuth 2,0-toegangs token aanvragen bij het micro soft Identity-platform (voorheen Azure AD). Azure AD verifieert de beveiligingsprincipal (een gebruiker, groep of Service-Principal) die de toepassing uitvoert. Als de verificatie slaagt, retourneert Azure AD het toegangs token voor de toepassing en kan de toepassing vervolgens het toegangs token gebruiken om aanvragen voor Azure Blob Storage of de wachtrij opslag te autoriseren.

In dit artikel wordt beschreven hoe u uw systeem eigen toepassing of webtoepassing configureert voor verificatie met micro soft Identity platform 2,0. Het code voorbeeld bevat .NET, maar andere talen gebruiken een vergelijk bare methode. Zie [overzicht van micro soft Identity platform (v 2.0)](../../active-directory/develop/v2-overview.md)voor meer informatie over micro soft identity platform 2,0.

Zie [toegang tot Azure Active Directory webtoepassingen verlenen met behulp van de oauth 2,0 code Grant flow](../../active-directory/develop/v2-oauth2-auth-code-flow.md)voor een overzicht van de OAuth 2,0-code subsidie stroom.

## <a name="assign-a-role-to-an-azure-ad-security-principal"></a>Een rol toewijzen aan een Azure AD-beveiligings-principal

Als u een beveiligingsprincipal van uw Azure Storage-toepassing wilt verifiëren, moet u eerst instellingen voor op rollen gebaseerde toegangs beheer (RBAC) voor die beveiligingsprincipal configureren. Azure Storage definieert ingebouwde RBAC-rollen die machtigingen voor containers en wacht rijen omvatten. Wanneer de RBAC-rol is toegewezen aan een beveiligingsprincipal, wordt die beveiligingsprincipal toegang verleend tot die bron. Zie [toegangs rechten voor Azure Blob en wachtrij gegevens beheren met RBAC](storage-auth-aad-rbac.md)voor meer informatie.

## <a name="register-your-application-with-an-azure-ad-tenant"></a>Uw toepassing registreren bij een Azure AD-Tenant

De eerste stap bij het gebruik van Azure AD om toegang tot opslag resources te autoriseren, is het registreren van uw client toepassing met een Azure AD-Tenant vanuit het [Azure Portal](https://portal.azure.com). Wanneer u uw client toepassing registreert, geeft u informatie over de toepassing op in azure AD. Azure AD biedt vervolgens een client-ID (ook wel een *toepassings-id*genoemd) die u gebruikt om uw toepassing te koppelen aan Azure ad tijdens runtime. Zie voor meer informatie over de client-ID [toepassings-en Service-Principal-objecten in azure Active Directory](../../active-directory/develop/app-objects-and-service-principals.md).

Als u uw Azure Storage-toepassing wilt registreren, volgt u de stappen in [Quick Start: een toepassing registreren bij het micro soft Identity-platform](../../active-directory/develop/quickstart-configure-app-access-web-apis.md). De volgende afbeelding toont algemene instellingen voor het registreren van een webtoepassing:

![Scherm afbeelding die laat zien hoe u uw opslag toepassing registreert bij Azure AD](./media/storage-auth-aad-app/app-registration.png)

> [!NOTE]
> Als u uw toepassing registreert als een systeem eigen toepassing, kunt u een geldige URI voor de **omleidings-URI**opgeven. Voor systeem eigen toepassingen hoeft deze waarde geen echte URL te zijn. Voor webtoepassingen moet de omleidings-URI een geldige URI zijn, omdat deze de URL specificeert waarnaar de tokens worden opgegeven.

Nadat u uw toepassing hebt geregistreerd, ziet u de toepassings-ID (of client-ID) onder **instellingen**:

![Scherm opname met de client-ID](./media/storage-auth-aad-app/app-registration-client-id.png)

Zie [toepassingen integreren met Azure Active Directory](../../active-directory/develop/quickstart-v2-register-an-app.md)voor meer informatie over het registreren van een toepassing met Azure AD.

## <a name="grant-your-registered-app-permissions-to-azure-storage"></a>Uw geregistreerde app-machtigingen verlenen aan Azure Storage

Verleen vervolgens de machtigingen van uw toepassing om Azure Storage-Api's aan te roepen. Met deze stap kan uw toepassing aanvragen voor Azure Storage met Azure AD autoriseren.

1. Selecteer op de pagina **overzicht** voor uw geregistreerde toepassing de optie **API-machtigingen weer geven**.
1. Selecteer in de sectie **API-machtigingen** de optie **een machtiging toevoegen** en kies **micro soft-api's**.
1. Selecteer **Azure Storage** in de lijst met resultaten om het deel venster **API-machtigingen voor aanvragen** weer te geven.
1. Onder **welk type machtigingen heeft uw toepassing nodig?** Houd er rekening mee dat het beschik bare machtigings type **gedelegeerde machtigingen**is. Deze optie is standaard voor u geselecteerd.
1. Schakel in de sectie **machtigingen selecteren** van het deel venster **API-machtigingen voor aanvragen** het selectie vakje in naast **user_impersonation**en klik vervolgens op **machtigingen toevoegen**.

    ![Scherm opname met machtigingen voor opslag](media/storage-auth-aad-app/registered-app-permissions-1.png)

In het deel venster **API-machtigingen** ziet u nu dat uw geregistreerde Azure AD-toepassing toegang heeft tot zowel Microsoft Graph als de Azure Storage. Er worden automatisch machtigingen verleend aan Microsoft Graph wanneer u uw app voor het eerst registreert bij Azure AD.

![Scherm opname van het registreren van app-machtigingen](media/storage-auth-aad-app/registered-app-permissions-2.png)

## <a name="create-a-client-secret"></a>Een client geheim maken

De toepassing heeft een client geheim nodig om de identiteit ervan te bewijzen wanneer een token wordt aangevraagd. Voer de volgende stappen uit om het client geheim toe te voegen:

1. Ga naar de registratie van uw app in de Azure Portal.
1. Selecteer de instelling **certificaten & geheimen** .
1. Klik onder **client geheimen**op **Nieuw client geheim** om een nieuw geheim te maken.
1. Geef een beschrijving op voor het geheim en kies het gewenste verloop interval.
1. Kopieer de waarde van het nieuwe geheim direct naar een veilige locatie. De volledige waarde wordt slechts één keer weer gegeven.

    ![Scherm opname van client geheim](media/storage-auth-aad-app/client-secret.png)

## <a name="client-libraries-for-token-acquisition"></a>Client bibliotheken voor het verkrijgen van tokens

Nadat u uw toepassing hebt geregistreerd en de machtiging hebt verleend om toegang te krijgen tot gegevens in Azure Blob-opslag of wachtrij opslag, kunt u code toevoegen aan uw toepassing om een beveiligingsprincipal te verifiëren en een OAuth 2,0-token te verkrijgen. Als u het token wilt verifiëren en verkrijgen, kunt u een van de [micro soft-identiteits platform verificatie bibliotheken](../../active-directory/develop/reference-v2-libraries.md) of een andere open-source-bibliotheek gebruiken die ondersteuning biedt voor openid connect Connect 1,0. Uw toepassing kan vervolgens het toegangs token gebruiken om een aanvraag voor Azure Blob-opslag of wachtrij opslag te autoriseren.

Zie de sectie [verificatie stromen](/en-us/azure/active-directory/develop/msal-authentication-flows) van de inhoud van de [micro soft-verificatie bibliotheek](/azure/active-directory/develop/msal-overview)voor een lijst met scenario's waarvoor het verkrijgen van tokens wordt ondersteund.

## <a name="well-known-values-for-authentication-with-azure-ad"></a>Bekende waarden voor verificatie met Azure AD

Als u een beveiligingsprincipal met Azure AD wilt verifiëren, moet u een aantal bekende waarden in uw code toevoegen.

### <a name="azure-ad-authority"></a>Azure AD-instantie

Voor de open bare cloud van micro soft is de basis-Azure AD-instantie als volgt, waarbij *Tenant-id* uw Active Directory Tenant-id (of directory-id) is:

`https://login.microsoftonline.com/<tenant-id>/`

Met de Tenant-ID wordt de Azure AD-Tenant geïdentificeerd die moet worden gebruikt voor verificatie. Dit wordt ook wel de Directory-ID genoemd. Als u de Tenant-ID wilt ophalen, gaat u naar de **overzichts** pagina voor de registratie van uw app in de Azure Portal en kopieert u de waarde daar.

### <a name="azure-storage-resource-id"></a>Resource-ID Azure Storage

[!INCLUDE [storage-resource-id-include](../../../includes/storage-resource-id-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>.NET-code voorbeeld: een blok-Blob maken

Het code voorbeeld laat zien hoe u een toegangs token van Azure AD ophaalt. Het toegangs token wordt gebruikt om de opgegeven gebruiker te verifiëren en vervolgens een aanvraag voor het maken van een blok-BLOB te autoriseren. Als u dit voor beeld wilt gebruiken, volgt u eerst de stappen die in de voor gaande secties worden beschreven.

Als u het token wilt aanvragen, hebt u de volgende waarden nodig van de registratie van uw app:

- De naam van uw Azure AD-domein. Haal deze waarde op op de pagina **overzicht** van uw Azure Active Directory.
- De Tenant-ID (of map). Haal deze waarde op op de pagina **overzicht** van de app-registratie.
- De client-ID (of toepassing). Haal deze waarde op op de pagina **overzicht** van de app-registratie.
- De URI van de omleiding van de client. Haal deze waarde op uit de **verificatie** -instellingen voor de registratie van uw app.
- De waarde van het client geheim. Haal deze waarde op van de locatie waarnaar u deze eerder hebt gekopieerd.

### <a name="create-a-storage-account-and-container"></a>Een opslag account en een container maken

Als u het code voorbeeld wilt uitvoeren, maakt u een opslag account in hetzelfde abonnement als uw Azure Active Directory. Maak vervolgens een container in dat opslag account. Met de voorbeeld code wordt een blok-Blob in deze container gemaakt.

Wijs vervolgens expliciet de rol **Storage BLOB data Inzender** toe aan het gebruikers account waaronder u de voorbeeld code gaat uitvoeren. Zie voor instructies over het toewijzen van deze rol in de Azure Portal [toegang verlenen aan Azure Blob en gegevens wachtrij met RBAC in de Azure Portal](storage-auth-aad-rbac-portal.md).

> [!NOTE]
> Wanneer u een Azure Storage-account maakt, worden er niet automatisch machtigingen toegewezen om toegang te krijgen tot gegevens via Azure AD. U moet uzelf expliciet een RBAC-rol toewijzen voor Azure Storage. U kunt deze toewijzen op het niveau van uw abonnement, resource groep, opslag account of container of wachtrij.

### <a name="create-a-web-application-that-authorizes-access-to-blob-storage-with-azure-ad"></a>Een webtoepassing maken die de toegang tot Blob Storage machtigt met Azure AD

Wanneer uw toepassing toegang heeft tot Azure Storage, gebeurt dit namens de gebruiker, dat wil zeggen dat BLOB-of wachtrij bronnen worden geopend met de machtigingen van de gebruiker die is aangemeld. Als u dit code voorbeeld wilt uitproberen, hebt u een webtoepassing nodig die de gebruiker vraagt om zich aan te melden met een Azure AD-identiteit. U kunt uw eigen app maken of de voorbeeld toepassing van micro soft gebruiken.

Een voltooide voor beeld-webtoepassing waarmee een token wordt opgehaald en wordt gebruikt voor het maken van een BLOB in Azure Storage is beschikbaar op [github](https://aka.ms/aadstorage). Het bekijken en uitvoeren van het voltooide voor beeld kan nuttig zijn bij het leren van de code voorbeelden. Zie de sectie [weer geven en het voltooide voor beeld uitvoeren](#view-and-run-the-completed-sample)voor instructies over het uitvoeren van het voltooide voor beeld.

#### <a name="add-references-and-using-statements"></a>Referenties toevoegen en instructies gebruiken  

Installeer de Azure Storage-client bibliotheek vanuit Visual Studio. Selecteer in het menu **Tools** de optie **NuGet Package Manager** en vervolgens **Package Manager Console**. Typ de volgende opdrachten in het console venster om de benodigde pakketten te installeren vanuit de Azure Storage-client bibliotheek voor .NET:

```console
Install-Package Microsoft.Azure.Storage.Blob
Install-Package Microsoft.Azure.Storage.Common
```

Voeg vervolgens de volgende using-instructies toe aan het HomeController.cs-bestand:

```csharp
using Microsoft.Identity.Client; //MSAL library for getting the access token
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
```

#### <a name="create-a-block-blob"></a>Een blok-Blob maken

Voeg het volgende code fragment toe om een blok-BLOB te maken:

```csharp
private static async Task<string> CreateBlob(string accessToken)
{
    // Create a blob on behalf of the user
    TokenCredential tokenCredential = new TokenCredential(accessToken);
    StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

    // Replace the URL below with your storage account URL
    CloudBlockBlob blob =
        new CloudBlockBlob(
            new Uri("https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt"),
            storageCredentials);
    await blob.UploadTextAsync("Blob created by Azure AD authenticated user.");
    return "Blob successfully created";
}
```

> [!NOTE]
> Als u BLOB-en wachtrij bewerkingen wilt autoriseren met een OAuth 2,0-token, moet u HTTPS gebruiken.

In het bovenstaande voor beeld verwerkt de .NET-client bibliotheek de autorisatie van de aanvraag om de blok-BLOB te maken. Azure Storage client bibliotheken voor andere talen, wordt ook de autorisatie van de aanvraag voor u afgehandeld. Als u echter een Azure Storage bewerking aanroept met een OAuth-token met behulp van de REST API, moet u de aanvraag machtigen met het OAuth-token.

Als u BLOB-en Queue-service bewerkingen wilt aanroepen met OAuth-toegangs tokens, geeft u het toegangs token in de **autorisatie** -header door middel van het **Bearer** -schema en geeft u een service versie van 2017-11-09 of hoger op, zoals wordt weer gegeven in het volgende voor beeld:

```https
GET /container/file.txt HTTP/1.1
Host: mystorageaccount.blob.core.windows.net
x-ms-version: 2017-11-09
Authorization: Bearer eyJ0eXAiOnJKV1...Xd6j
```

#### <a name="get-an-oauth-token-from-azure-ad"></a>Een OAuth-Token ophalen uit Azure AD

Voeg vervolgens een methode toe die een token aanvraagt uit Azure AD namens de gebruiker. Met deze methode wordt het bereik gedefinieerd waarvoor machtigingen moeten worden toegekend. Zie [machtigingen en toestemming in het micro soft Identity platform-eind punt](../../active-directory/develop/v2-permissions-and-consent.md)voor meer informatie over machtigingen en bereiken.

Gebruik de resource-ID om het bereik te maken waarvoor het token moet worden verkregen. In het voor beeld wordt het bereik gemaakt met behulp van de resource-ID in combi natie met het ingebouwde `user_impersonation` bereik. Dit geeft aan dat het token wordt aangevraagd namens de gebruiker.

Houd er rekening mee dat u de gebruiker mogelijk moet presen teren met een interface die de gebruiker in staat stelt om toestemming te geven om de tokens in hun naam aan te vragen. Wanneer toestemming nodig is, wordt de **MsalUiRequiredException** door het voor beeld onderschept en wordt een andere methode aangeroepen om de aanvraag voor toestemming te vergemakkelijken:

```csharp
public async Task<IActionResult> Blob()
{
    var scopes = new string[] { "https://storage.azure.com/user_impersonation" };
    try
    {
        var accessToken =
            await _tokenAcquisition.GetAccessTokenOnBehalfOfUser(HttpContext, scopes);
        ViewData["Message"] = await CreateBlob(accessToken);
        return View();
    }
    catch (MsalUiRequiredException ex)
    {
        AuthenticationProperties properties =
            BuildAuthenticationPropertiesForIncrementalConsent(scopes, ex);
        return Challenge(properties);
    }
}
```

Toestemming is het proces van een gebruiker die toestemming verleent voor toegang tot beveiligde resources voor hun naam. Het micro soft Identity-platform 2,0 ondersteunt incrementele toestemming, wat inhoudt dat een beveiligingsprincipal in eerste instantie een minimale set machtigingen kan aanvragen en zo nodig machtigingen kan toevoegen in de loop van de tijd. Wanneer uw code een toegangs token aanvraagt, geeft u het bereik van machtigingen op dat uw app op een bepaald moment nodig heeft in de `scope` para meter. Zie de sectie met de titel **incrementele en dynamische toestemming** in [Waarom update to micro soft Identity platform (v 2.0)? (Engelstalig)](../../active-directory/azuread-dev/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent)voor meer informatie over incrementele toestemming.

De volgende methode bouwt de verificatie-eigenschappen voor het aanvragen van een incrementele toestemming:

```csharp
private AuthenticationProperties BuildAuthenticationPropertiesForIncrementalConsent(string[] scopes,
                                                                                    MsalUiRequiredException ex)
{
    AuthenticationProperties properties = new AuthenticationProperties();

    // Set the scopes, including the scopes that ADAL.NET or MSAL.NET need for the Token cache.
    string[] additionalBuildInScopes = new string[] { "openid", "offline_access", "profile" };
    properties.SetParameter<ICollection<string>>(OpenIdConnectParameterNames.Scope,
                                                 scopes.Union(additionalBuildInScopes).ToList());

    // Attempt to set the login_hint so that the logged-in user is not presented
    // with an account selection dialog.
    string loginHint = HttpContext.User.GetLoginHint();
    if (!string.IsNullOrWhiteSpace(loginHint))
    {
        properties.SetParameter<string>(OpenIdConnectParameterNames.LoginHint, loginHint);

        string domainHint = HttpContext.User.GetDomainHint();
        properties.SetParameter<string>(OpenIdConnectParameterNames.DomainHint, domainHint);
    }

    // Specify any additional claims that are required (for instance, MFA).
    if (!string.IsNullOrEmpty(ex.Claims))
    {
        properties.Items.Add("claims", ex.Claims);
    }

    return properties;
}
```

## <a name="view-and-run-the-completed-sample"></a>Het voltooide voor beeld weer geven en uitvoeren

Als u de voorbeeld toepassing wilt uitvoeren, moet u deze eerst klonen of downloaden via [github](https://github.com/Azure-Samples/storage-dotnet-azure-ad-msal). Werk vervolgens de toepassing bij zoals beschreven in de volgende secties.

### <a name="provide-values-in-the-settings-file"></a>Waarden opgeven in het instellingen bestand

Werk vervolgens als volgt het bestand *appSettings. json* bij met uw eigen waarden:

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "<azure-ad-domain-name>.onmicrosoft.com",
    "TenantId": "<tenant-id>",
    "ClientId": "<client-id>",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc",

    // To call an API
    "ClientSecret": "<client-secret>"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="update-the-storage-account-and-container-name"></a>Het opslag account en de container naam bijwerken

Werk in het *HomeController.cs* -bestand de URI bij die naar de blok-BLOB verwijst om de naam van uw opslag account en container te gebruiken:

```csharp
CloudBlockBlob blob = new CloudBlockBlob(
                      new Uri("https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt"),
                      storageCredentials);
```

### <a name="enable-implicit-grant-flow"></a>Impliciete toekennings stroom inschakelen

Als u het voor beeld wilt uitvoeren, moet u mogelijk de impliciete toekennings stroom voor uw app-registratie configureren. Volg deze stappen:

1. Ga naar de registratie van uw app in de Azure Portal.
1. Selecteer de **verificatie** -instelling in de sectie beheren.
1. Schakel onder **Geavanceerde instellingen**in het gedeelte **impliciete toekenning** de selectie vakjes in om toegangs tokens en id-tokens in te scha kelen, zoals wordt weer gegeven in de volgende afbeelding:

    ![Scherm afbeelding die laat zien hoe instellingen voor impliciete toekennings stroom worden ingeschakeld](media/storage-auth-aad-app/enable-implicit-grant-flow.png)

### <a name="update-the-port-used-by-localhost"></a>De poort die wordt gebruikt door localhost bijwerken

Wanneer u het voor beeld uitvoert, is het mogelijk dat u de omleidings-URI die is opgegeven in de app-registratie, moet bijwerken om de *localhost* -poort die tijdens runtime is toegewezen, te gebruiken. Voer de volgende stappen uit om de omleidings-URI bij te werken om de toegewezen poort te gebruiken:

1. Ga naar de registratie van uw app in de Azure Portal.
1. Selecteer de **verificatie** -instelling in de sectie beheren.
1. Bewerk de poort onder **omleidings-uri's**, zoals wordt weer gegeven in de volgende afbeelding:

    ![Scherm opname van omleidings-Uri's voor app-registratie](media/storage-auth-aad-app/redirect-uri.png)

## <a name="next-steps"></a>Volgende stappen

- Zie [micro soft Identity platform](https://docs.microsoft.com/azure/active-directory/develop/)voor meer informatie over het micro soft-identiteits platform.
- Zie [toegangs rechten voor opslag gegevens beheren met RBAC](storage-auth-aad-rbac.md)voor meer informatie over RBAC-rollen voor Azure Storage.
- Voor meer informatie over het gebruik van beheerde identiteiten voor Azure-resources met Azure Storage raadpleegt [u toegang verifiëren tot blobs en wacht rijen met Azure Active Directory en beheerde identiteiten voor Azure-resources](storage-auth-aad-msi.md).
