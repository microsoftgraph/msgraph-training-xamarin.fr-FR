<!-- markdownlint-disable MD002 MD041 -->

Dans cet exercice, vous allez étendre l’application de l’exercice précédent pour prendre en charge l’authentification avec Azure AD. Cela est nécessaire pour obtenir le jeton d’accès OAuth nécessaire pour appeler Microsoft Graph. Dans cette étape, vous allez intégrer la [bibliothèque d’authentification Microsoft pour .net (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) dans l’application.

Dans **l’Explorateur de solutions**, développez le projet **GraphTutorial** , puis cliquez avec le bouton droit sur le dossier **modèles** . Sélectionnez **Ajouter une classe de >.**... Nommez la `OAuthSettings` classe et sélectionnez **Ajouter**. Ouvrez le fichier **OAuthSettings.cs** et remplacez son contenu par ce qui suit.

```cs
namespace GraphTutorial.Models
{
    public static class OAuthSettings
    {
        public const string ApplicationId = "YOUR_APP_ID_HERE";
        public const string Scopes = "User.Read Calendars.Read";
    }
}
```

Remplacez `YOUR_APP_ID_HERE` par l’ID d’application de l’inscription de votre application.

> [!IMPORTANT]
> Si vous utilisez le contrôle de code source tel que git, il est maintenant recommandé d’exclure le `OAuthSettings.cs` fichier du contrôle de code source afin d’éviter une fuite accidentelle de votre ID d’application.

## <a name="implement-sign-in"></a>Mettre en œuvre la connexion

Ouvrez le fichier **app.Xaml.cs** dans le projet **GraphTutorial** et ajoutez les instructions suivantes `using` en haut du fichier.

```cs
using GraphTutorial.Models;
using Microsoft.Identity.Client;
using Microsoft.Graph;
using System.Diagnostics;
using System.Linq;
using System.Net.Http.Headers;
```

Modifiez la ligne de déclaration de classe d' **application** pour résoudre le conflit de nom pour l' **application**.

```cs
public partial class App : Xamarin.Forms.Application, INotifyPropertyChanged
```

Ajoutez les propriétés suivantes à la `App` classe.

```cs
// UIParent used by Android version of the app
public static object AuthUIParent = null;

// Keychain security group used by iOS version of the app
public static string iOSKeychainSecurityGroup = null;

// Microsoft Authentication client for native/mobile apps
public static IPublicClientApplication PCA;

// Microsoft Graph client
public static GraphServiceClient GraphClient;

// Microsoft Graph permissions used by app
private readonly string[] Scopes = OAuthSettings.Scopes.Split(' ');
```

Ensuite, créez un nouveau `PublicClientApplication` dans le constructeur de la `App` classe.

```cs
public App()
{
    InitializeComponent();

    var builder = PublicClientApplicationBuilder
        .Create(OAuthSettings.ApplicationId);

    if (!string.IsNullOrEmpty(iOSKeychainSecurityGroup))
    {
        builder = builder.WithIosKeychainSecurityGroup(iOSKeychainSecurityGroup);
    }

    PCA = builder.Build();

    MainPage = new MainPage();
}
```

À présent, `SignIn` mettez à jour la `PublicClientApplication` fonction pour utiliser l’pour obtenir un jeton d’accès. Ajoutez le code suivant au- `await GetUserInfo();` dessus de la ligne.

```cs
// First, attempt silent sign in
// If the user's information is already in the app's cache,
// they won't have to sign in again.
try
{
    var accounts = await PCA.GetAccountsAsync();

    var silentAuthResult = await PCA
        .AcquireTokenSilent(Scopes, accounts.FirstOrDefault())
        .ExecuteAsync();

    Debug.WriteLine("User already signed in.");
    Debug.WriteLine($"Successful silent authentication for: {silentAuthResult.Account.Username}");
    Debug.WriteLine($"Access token: {silentAuthResult.AccessToken}");
}
catch (MsalUiRequiredException msalEx)
{
    // This exception is thrown when an interactive sign-in is required.
    Debug.WriteLine("Silent token request failed, user needs to sign-in: " + msalEx.Message);
    // Prompt the user to sign-in
    var interactiveRequest = PCA.AcquireTokenInteractive(Scopes);

    if (AuthUIParent != null)
    {
        interactiveRequest = interactiveRequest
            .WithParentActivityOrWindow(AuthUIParent);
    }

    var interactiveAuthResult = await interactiveRequest.ExecuteAsync();
    Debug.WriteLine($"Successful interactive authentication for: {interactiveAuthResult.Account.Username}");
    Debug.WriteLine($"Access token: {interactiveAuthResult.AccessToken}");
}
catch (Exception ex)
{
    Debug.WriteLine("Authentication failed. See exception messsage for more details: " + ex.Message);
}
```

Ce code tente d’abord d’obtenir un jeton d’accès en mode silencieux. Si les informations d’un utilisateur figurent déjà dans le cache de l’application (par exemple, si l’utilisateur a fermé l’application précédemment sans se déconnecter), cette opération réussit et il n’y a aucune raison d’inviter l’utilisateur. S’il n’y a pas d’informations d’utilisateur dans le cache `AcquireTokenSilent().ExecuteAsync()` , la fonction génère `MsalUiRequiredException`un. Dans ce cas, le code appelle la fonction interactive pour obtenir un jeton, `AcquireTokenInteractive`.

À présent, `SignOut` mettez à jour la fonction pour supprimer les informations de l’utilisateur du cache. Ajoutez le code suivant au début de la `SignOut` fonction.

```cs
// Get all cached accounts for the app
// (Should only be one)
var accounts = await PCA.GetAccountsAsync();
while (accounts.Any())
{
    // Remove the account info from the cache
    await PCA.RemoveAsync(accounts.First());
    accounts = await PCA.GetAccountsAsync();
}
```

### <a name="update-android-project-to-enable-sign-in"></a>Mettre à jour le projet Android pour activer la connexion

Lorsqu’elle est utilisée dans un projet Android Xamarin, la bibliothèque d’authentification Microsoft a quelques [exigences spécifiques à Android](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-Android-specifics).

Tout d’abord, vous devez mettre à jour le manifeste Android pour enregistrer l’URI de redirection. Dans le projet **GraphTutorial. Android** , développez le dossier **Propriétés** , puis ouvrez **AndroidManifest. xml**. Si vous utilisez Visual Studio pour Mac, basculez en mode **source** à l’aide des onglets situés en bas du fichier. Remplacez tout le contenu par ce qui suit.

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android" android:versionCode="1" android:versionName="1.0" package="com.companyname.GraphTutorial">
    <uses-sdk android:minSdkVersion="21" android:targetSdkVersion="28" />
    <application android:label="GraphTutorial.Android">
        <activity android:name="microsoft.identity.client.BrowserTabActivity">
            <intent-filter>
                <action android:name="android.intent.action.VIEW" />
                <category android:name="android.intent.category.DEFAULT" />
                <category android:name="android.intent.category.BROWSABLE" />
                <data android:scheme="msalYOUR_APP_ID_HERE" android:host="auth" />
            </intent-filter>
        </activity>
    </application>
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.INTERNET" />
</manifest>
```

Remplacez `YOUR_APP_ID_HERE` par l’ID de votre application dans l’inscription de votre application.

Ensuite, ouvrez **MainActivity.cs** et ajoutez les instructions `using` suivantes en haut du fichier.

```cs
using Microsoft.Identity.Client;
using Android.Content;
```

Ensuite, substituez la `OnActivityResult` fonction pour transmettre le contrôle à la bibliothèque MSAL. Ajoutez les éléments suivants à `MainActivity` la classe.

```cs
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);
    AuthenticationContinuationHelper
        .SetAuthenticationContinuationEventArgs(requestCode, resultCode, data);
}
```

Enfin, dans la `OnCreate` fonction, ajoutez la ligne suivante après la `LoadApplication(new App());` ligne.

```cs
App.AuthUIParent = this;
```

### <a name="update-ios-project-to-enable-sign-in"></a>Mettre à jour le projet iOS pour activer la connexion

> [!IMPORTANT]
> Étant donné que MSAL nécessite l’utilisation d’un fichier habilitations. plist, vous devez configurer Visual Studio avec votre compte de développeur Apple pour activer la mise en service. Si vous exécutez ce didacticiel dans le simulateur iPhone, vous devez ajouter les **habilitations. plist** dans le champ **habilitations personnalisées** dans les paramètres du projet **GraphTutorial. iOS** , **signature du package de >iOS**. Pour plus d’informations, consultez la rubrique [mise en service de l’appareil pour Xamarin. iOS](/xamarin/ios/get-started/installation/device-provisioning).

Lorsqu’elle est utilisée dans un projet iOS Xamarin, la bibliothèque d’authentification Microsoft a quelques [exigences spécifiques à IOS](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-iOS-specifics).

Tout d’abord, vous devez activer l’accès à la chaîne de trousseau. Dans l’Explorateur de solutions, développez le projet **GraphTutorial. iOS** , puis ouvrez le fichier **habilitations. plist** . Localisez le **trousseau** d’autorisations, puis sélectionnez **activer la chaîne de trousseau**. Dans les **groupes de trousseau**, ajoutez une entrée au format `com.YOUR_DOMAIN.GraphTutorial`.

![Capture d’écran de la configuration de la habilitation de trousseau](./images/enable-keychain-access.png)

Ensuite, vous devez enregistrer l’URI de redirection MSAL par défaut en tant que type d’URL géré par votre application. Ouvrez le fichier **info. plist** et effectuez les modifications suivantes.

- Dans l’onglet **application** , vérifiez que la valeur de l' **identificateur groupé** correspond à la valeur que vous avez définie pour les **groupes de trousseau** dans les **habilitations. plist**. Si ce n’est pas le cas, mettez-le à jour maintenant.
- Sous l’onglet **avancé** , recherchez la section **types d’URL** . Ajoutez un type d’URL ici avec les valeurs suivantes :
  - **Identificateur**: défini sur la valeur de votre **identificateur de package**
  - **Modèles d’URL**: définis `msal{YOUR-APP-ID}`sur. Par exemple, si votre ID d’application `67ad5eba-0cfc-414d-8f9f-0a6d973a907c`est, définissez-le sur `msal67ad5eba-0cfc-414d-8f9f-0a6d973a907c`.
  - **Rôle**:`Editor`
  - **Icon**: laisser vide

![Capture d’écran de la section types d’URL de info. plist](./images/add-url-type.png)

Enfin, mettez à jour le code dans le projet **GraphTutorial. iOS** pour gérer la redirection lors de l’authentification. Ouvrez le fichier **AppDelegate.cs** et ajoutez l’instruction `using` suivante en haut du fichier.

```cs
using Microsoft.Identity.Client;
```

Ajoutez la ligne suivante pour `FinishedLaunching` fonctionner juste avant la `LoadApplication(new App());` ligne.

```cs
// Specify the Keychain access group
App.iOSKeychainSecurityGroup = NSBundle.MainBundle.BundleIdentifier;
```

Enfin, remplacez la `OpenUrl` fonction pour transmettre l’URL à la bibliothèque MSAL. Ajoutez les éléments suivants à `AppDelegate` la classe.

```cs
// Handling redirect URL
// See: https://github.com/azuread/microsoft-authentication-library-for-dotnet/wiki/Xamarin-iOS-specifics
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

## <a name="storing-the-tokens"></a>Stockage des jetons

Lorsque la bibliothèque d’authentification Microsoft est utilisée dans un projet Xamarin, elle tire parti du stockage sécurisé natif pour mettre en cache les jetons par défaut. Pour plus d’informations, consultez [la rubrique sérialisation du cache de jetons](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization) .

## <a name="test-sign-in"></a>Tester la connexion

À ce stade, si vous exécutez l’application et que vous appuyez sur le bouton **de connexion** , vous êtes invité à vous connecter. Lors de la connexion, le jeton d’accès doit s’afficher dans la sortie du débogueur.

![Capture d’écran de la fenêtre sortie dans Visual Studio](./images/debugger-access-token.png)

## <a name="get-user-details"></a>Obtenir les détails de l’utilisateur

Ajoutez une nouvelle fonction à la classe **app** pour initialiser le `GraphServiceClient`.

```cs
private async Task InitializeGraphClientAsync()
{
    var currentAccounts = await PCA.GetAccountsAsync();
    try
    {
        if (currentAccounts.Count() > 0)
        {
            // Initialize Graph client
            GraphClient = new GraphServiceClient(new DelegateAuthenticationProvider(
                async (requestMessage) =>
                {
                    var result = await PCA.AcquireTokenSilent(Scopes, currentAccounts.FirstOrDefault())
                        .ExecuteAsync();

                    requestMessage.Headers.Authorization =
                        new AuthenticationHeaderValue("Bearer", result.AccessToken);
                }));

            await GetUserInfo();

            IsSignedIn = true;
        }
        else
        {
            IsSignedIn = false;
        }
    }
    catch(Exception ex)
    {
        Debug.WriteLine(
            $"Failed to initialized graph client. Accounts in the msal cache: {currentAccounts.Count()}. See exception message for details: {ex.Message}");
    }
}
```

À présent, `SignIn` mettez à jour la fonction dans **app.Xaml.cs** pour appeler `GetUserInfo`cette fonction au lieu de. Supprimez les éléments suivants `SignIn` de la fonction.

```cs
await GetUserInfo();

IsSignedIn = true;
```

Ajoutez le code suivant à la fin de `SignIn` la fonction.

```cs
await InitializeGraphClientAsync();
```

À présent, `GetUserInfo` mettez à jour la fonction pour obtenir les détails de l’utilisateur à partir de Microsoft Graph. Remplacez la fonction `GetUserInfo` existante par ce qui suit.

```cs
private async Task GetUserInfo()
{
    // Get the logged on user's profile (/me)
    var user = await GraphClient.Me.Request().GetAsync();

    UserPhoto = ImageSource.FromStream(() => GetUserPhoto());
    UserName = user.DisplayName;
    UserEmail = string.IsNullOrEmpty(user.Mail) ? user.UserPrincipalName : user.Mail;
}
```

Si vous enregistrez vos modifications et exécutez l’application maintenant, une fois connecté, l’interface utilisateur est mise à jour avec le nom d’affichage et l’adresse de messagerie de l’utilisateur.
