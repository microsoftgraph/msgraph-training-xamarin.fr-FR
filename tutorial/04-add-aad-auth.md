<!-- markdownlint-disable MD002 MD041 -->

Dans cet exercice, vous allez étendre l’application de l’exercice précédent pour prendre en charge l’authentification avec Azure AD. Cette étape est nécessaire pour obtenir le jeton d’accès OAuth nécessaire pour appeler Microsoft Graph. Dans cette étape, vous allez intégrer la bibliothèque d’authentification [Microsoft pour .NET (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) dans l’application.

1. Dans **l’Explorateur de** solutions, développez **le projet GraphTutorial** et cliquez avec le bouton droit sur **le dossier Modèles.** Sélectionnez **Ajouter > classe...**. Nommez la classe `OAuthSettings` et sélectionnez **Ajouter.**

1. Ouvrez **le OAuthSettings.cs** fichier et remplacez son contenu par ce qui suit.

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/Models/OAuthSettings.cs.example":::

1. Remplacez `YOUR_APP_ID_HERE` par l’ID d’application de l’inscription de votre application.

    > [!IMPORTANT]
    > Si vous utilisez un contrôle source tel que Git, il est temps d’exclure le fichier du contrôle source afin d’éviter toute fuite accidentelle de votre `OAuthSettings.cs` ID d’application.

## <a name="implement-sign-in"></a>Implémentation de la connexion

1. Ouvrez **le App.xaml.cs** dans le projet **GraphTutorial** et ajoutez les `using` instructions suivantes en haut du fichier.

    ```csharp
    using GraphTutorial.Models;
    using Microsoft.Identity.Client;
    using Microsoft.Graph;
    using System.Diagnostics;
    using System.Linq;
    using System.Net.Http.Headers;
    using TimeZoneConverter;
    ```

1. Modifiez la **ligne de déclaration** de classe d’application pour résoudre le conflit de noms pour **Application**.

    ```csharp
    public partial class App : Xamarin.Forms.Application, INotifyPropertyChanged
    ```

1. Ajoutez les propriétés suivantes à la `App` classe.

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="AuthPropertiesSnippet":::z

1. Ensuite, créez une `PublicClientApplication` nouvelle classe dans le constructeur de la `App` classe.

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="AppConstructorSnippet" highlight="5-14":::

1. Mettez à jour `SignIn` la fonction pour utiliser la fonction pour obtenir un `PublicClientApplication` jeton d’accès. Ajoutez le code suivant au-dessus de la `await GetUserInfo();` ligne.

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="GetTokenSnippet":::

    Ce code tente d’abord d’obtenir un jeton d’accès en mode silencieux. Si les informations d’un utilisateur se trouve déjà dans le cache de l’application (par exemple, si l’utilisateur a fermé l’application précédemment sans se désener), cela réussit et il n’y a aucune raison d’en informer l’utilisateur. S’il n’y a pas d’informations d’utilisateur dans le cache, la `AcquireTokenSilent().ExecuteAsync()` fonction envoie un `MsalUiRequiredException` . Dans ce cas, le code appelle la fonction interactive pour obtenir un `AcquireTokenInteractive` jeton.

1. Mettez à `SignOut` jour la fonction pour supprimer les informations de l’utilisateur du cache. Ajoutez le code suivant au début de la `SignOut` fonction.

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="RemoveAccountSnippet":::

### <a name="update-android-project-to-enable-sign-in"></a>Mettre à jour le projet Android pour activer la connectez-vous

Lorsqu’elle est utilisée dans un projet Xamarin Android, la bibliothèque d’authentification Microsoft présente quelques exigences [spécifiques à Android.](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-Android-specifics)

1. Dans **le projet GraphTutorial.Android,** développez le dossier **Propriétés,** puis **ouvrezAndroidManifest.xml**. Si vous utilisez Visual Studio pour Mac, cliquez surAndroidManifest.xmlpuis choisissez **Ouvrir** **avec,** puis **Éditeur de code source.** Remplacez tout le contenu par ce qui suit.

    :::code language="xml" source="../demo/GraphTutorial/GraphTutorial.Android/Properties/AndroidManifest.xml":::

1. Ouvrez **MainActivity.cs** et ajoutez les `using` instructions suivantes en haut du fichier.

    ```csharp
    using Android.Content;
    using Microsoft.Identity.Client;
    ```

1. Remplacez la `OnActivityResult` fonction pour passer le contrôle à la bibliothèque MSAL. Ajoutez ce qui suit à la `MainActivity` classe.

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial.Android/MainActivity.cs" id="OnActivityResultSnippet":::

1. Dans la `OnCreate` fonction, ajoutez la ligne suivante après la `LoadApplication(new App());` ligne.

    ```csharp
    App.AuthUIParent = this;
    ```

### <a name="update-ios-project-to-enable-sign-in"></a>Mettre à jour le projet iOS pour activer la connectez-vous

> [!IMPORTANT]
> Étant donné que MSAL nécessite l’utilisation d’un fichier Entitlements.plist, vous devez configurer Visual Studio avec votre compte de développeur Apple pour activer l’approvisionnement. Si vous exécutez ce didacticiel dans le simulateur iPhone, vous devez ajouter **Entitlements.plist** dans le champ Droits personnalisés dans les **paramètres** du projet **GraphTutorial.iOS,** **Build->iOS Bundle Signing**. Pour plus d’informations, [voir Device provisioning for Xamarin.iOS](/xamarin/ios/get-started/installation/device-provisioning).

Lorsqu’elle est utilisée dans un projet IOS Xamarin, la bibliothèque d’authentification Microsoft a quelques exigences [spécifiques à iOS.](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-iOS-specifics)

1. Dans l’Explorateur de solutions, développez **le projet GraphTutorial.iOS,** puis ouvrez le fichier **Entitlements.plist.**

1. Recherchez **le droit auchain,** puis **sélectionnez Activer lechain.**

1. Dans **les groupes dechains,** ajoutez une entrée au format `com.companyname.GraphTutorial` .

    ![Capture d’écran de la configuration des droits duchain](./images/enable-keychain-access.png)

1. Mettez à jour le code dans **le projet GraphTutorial.iOS** pour gérer la redirection pendant l’authentification. Ouvrez **AppDelegate.cs** fichier et ajoutez l’instruction `using` suivante en haut du fichier.

    ```csharp
    using Microsoft.Identity.Client;
    ```

1. Ajoutez la ligne suivante pour `FinishedLaunching` qu’elle fonctionne juste avant la `LoadApplication(new App());` ligne.

    ```csharp
    // Specify the Keychain access group
    App.iOSKeychainSecurityGroup = NSBundle.MainBundle.BundleIdentifier;
    ```

1. Remplacez la `OpenUrl` fonction pour transmettre l’URL à la bibliothèque MSAL. Ajoutez ce qui suit à la `AppDelegate` classe.

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial.iOS/AppDelegate.cs" id="OpenUrlSnippet":::

## <a name="storing-the-tokens"></a>Stockage des jetons

Lorsque la bibliothèque d’authentification Microsoft est utilisée dans un projet Xamarin, elle tire parti du stockage sécurisé natif pour mettre en cache les jetons par défaut. Pour plus [d’informations, voir sérialisation](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization) du cache de jetons.

## <a name="test-sign-in"></a>Tester la sign-in

À ce stade, si vous  exécutez l’application et appuyez sur le bouton Se connectez, vous êtes invité à vous y inscrire. Si vous vous connectez avec succès, vous devez voir le jeton d’accès imprimé dans la sortie du débogger.

![Capture d’écran de la fenêtre Sortie dans Visual Studio](./images/debugger-access-token.png)

## <a name="get-user-details"></a>Obtenir les détails de l’utilisateur

1. Ajoutez une nouvelle fonction à la classe **App** pour initialiser le `GraphServiceClient` .

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="InitializeGraphClientSnippet":::

1. Mettez à `SignIn` jour la fonction dans **App.xaml.cs** pour appeler cette fonction au lieu de `GetUserInfo` . Supprimez ce qui suit de la `SignIn` fonction.

    ```csharp
    await GetUserInfo();

    IsSignedIn = true;
    ```

1. Ajoutez ce qui suit à la fin de la `SignIn` fonction.

    ```csharp
    await InitializeGraphClientAsync();
    ```

1. Mettez à `GetUserInfo` jour la fonction pour obtenir les détails de l’utilisateur à partir de Microsoft Graph. Remplacez la fonction `GetUserInfo` existante par ce qui suit.

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="GetUserInfoSnippet":::

1. Enregistrez vos modifications et exécutez l’application. Une fois que l’interface utilisateur est mise à jour avec le nom d’affichage et l’adresse e-mail de l’utilisateur.
