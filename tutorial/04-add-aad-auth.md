<!-- markdownlint-disable MD002 MD041 -->

Dans cet exercice, vous allez étendre l’application de l’exercice précédent pour prendre en charge l’authentification avec Azure AD. Cela est nécessaire pour obtenir le jeton d’accès OAuth nécessaire pour appeler Microsoft Graph. Dans cette étape, vous allez intégrer la [bibliothèque d’authentification Microsoft pour .net (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) dans l’application.

1. Dans **l’Explorateur de solutions**, développez le projet **GraphTutorial** , puis cliquez avec le bouton droit sur le dossier **modèles** . Sélectionnez **Ajouter une classe de >.**... Nommez la `OAuthSettings` classe et sélectionnez **Ajouter**.

1. Ouvrez le fichier **OAuthSettings.cs** et remplacez son contenu par ce qui suit.

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/Models/OAuthSettings.cs.example":::

1. Remplacez `YOUR_APP_ID_HERE` par l’ID d’application de l’inscription de votre application.

    > [!IMPORTANT]
    > Si vous utilisez le contrôle de code source tel que git, il est maintenant recommandé d’exclure le `OAuthSettings.cs` fichier du contrôle de code source afin d’éviter une fuite accidentelle de votre ID d’application.

## <a name="implement-sign-in"></a>Implémentation de la connexion

1. Ouvrez le fichier **app.Xaml.cs** dans le projet **GraphTutorial** et ajoutez les instructions suivantes `using` en haut du fichier.

    ```csharp
    using GraphTutorial.Models;
    using Microsoft.Identity.Client;
    using Microsoft.Graph;
    using System.Diagnostics;
    using System.Linq;
    using System.Net.Http.Headers;
    ```

1. Modifiez la ligne de déclaration de classe d' **application** pour résoudre le conflit de nom pour l' **application**.

    ```csharp
    public partial class App : Xamarin.Forms.Application, INotifyPropertyChanged
    ```

1. Ajoutez les propriétés suivantes à la `App` classe.

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="AuthPropertiesSnippet":::z

1. Ensuite, créez un nouveau `PublicClientApplication` dans le constructeur de la `App` classe.

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="AppConstructorSnippet" highlight="5-14":::

1. Mettez à `SignIn` jour la fonction pour `PublicClientApplication` utiliser l’pour obtenir un jeton d’accès. Ajoutez le code suivant au- `await GetUserInfo();` dessus de la ligne.

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="GetTokenSnippet":::

    Ce code tente d’abord d’obtenir un jeton d’accès en mode silencieux. Si les informations d’un utilisateur figurent déjà dans le cache de l’application (par exemple, si l’utilisateur a fermé l’application précédemment sans se déconnecter), cette opération réussit et il n’y a aucune raison d’inviter l’utilisateur. S’il n’y a pas d’informations d’utilisateur dans le cache `AcquireTokenSilent().ExecuteAsync()` , la fonction génère `MsalUiRequiredException`un. Dans ce cas, le code appelle la fonction interactive pour obtenir un jeton, `AcquireTokenInteractive`.

1. Mettez à `SignOut` jour la fonction pour supprimer les informations de l’utilisateur du cache. Ajoutez le code suivant au début de la `SignOut` fonction.

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="RemoveAccountSnippet":::

### <a name="update-android-project-to-enable-sign-in"></a>Mettre à jour le projet Android pour activer la connexion

Lorsqu’elle est utilisée dans un projet Android Xamarin, la bibliothèque d’authentification Microsoft a quelques [exigences spécifiques à Android](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-Android-specifics).

1. Dans le projet **GraphTutorial. Android** , développez le dossier **Propriétés** , puis ouvrez **AndroidManifest. xml**. Si vous utilisez Visual Studio pour Mac, Control Click **AndroidManifest. xml** et choisissez **Ouvrir avec**, puis **éditeur de code source**. Remplacez tout le contenu par ce qui suit.

    :::code language="xml" source="../demo/GraphTutorial/GraphTutorial.Android/Properties/AndroidManifest.xml":::

1. Ouvrez **MainActivity.cs** et ajoutez les instructions `using` suivantes en haut du fichier.

    ```csharp
    using Android.Content;
    using Microsoft.Identity.Client;
    ```

1. Remplacez la `OnActivityResult` fonction pour transmettre le contrôle à la bibliothèque MSAL. Ajoutez les éléments suivants à `MainActivity` la classe.

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial.Android/MainActivity.cs" id="OnActivityResultSnippet":::

1. Dans la `OnCreate` fonction, ajoutez la ligne suivante après la `LoadApplication(new App());` ligne.

    ```csharp
    App.AuthUIParent = this;
    ```

### <a name="update-ios-project-to-enable-sign-in"></a>Mettre à jour le projet iOS pour activer la connexion

> [!IMPORTANT]
> Étant donné que MSAL nécessite l’utilisation d’un fichier habilitations. plist, vous devez configurer Visual Studio avec votre compte de développeur Apple pour activer la mise en service. Si vous exécutez ce didacticiel dans le simulateur iPhone, vous devez ajouter les **habilitations. plist** dans le champ **habilitations personnalisées** dans les paramètres du projet **GraphTutorial. iOS** , **signature du package de >iOS**. Pour plus d’informations, consultez la rubrique [mise en service de l’appareil pour Xamarin. iOS](/xamarin/ios/get-started/installation/device-provisioning).

Lorsqu’elle est utilisée dans un projet iOS Xamarin, la bibliothèque d’authentification Microsoft a quelques [exigences spécifiques à IOS](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-iOS-specifics).

1. Dans l’Explorateur de solutions, développez le projet **GraphTutorial. iOS** , puis ouvrez le fichier **habilitations. plist** .

1. Localisez le **trousseau** d’autorisations, puis sélectionnez **activer la chaîne de trousseau**.

1. Dans les **groupes de trousseau**, ajoutez une entrée au format `com.companyname.GraphTutorial`.

    ![Capture d’écran de la configuration de la habilitation de trousseau](./images/enable-keychain-access.png)

1. Mettez à jour le code dans le projet **GraphTutorial. iOS** afin de gérer la redirection lors de l’authentification. Ouvrez le fichier **AppDelegate.cs** et ajoutez l’instruction `using` suivante en haut du fichier.

    ```csharp
    using Microsoft.Identity.Client;
    ```

1. Ajoutez la ligne suivante pour `FinishedLaunching` fonctionner juste avant la `LoadApplication(new App());` ligne.

    ```csharp
    // Specify the Keychain access group
    App.iOSKeychainSecurityGroup = NSBundle.MainBundle.BundleIdentifier;
    ```

1. Remplacez la `OpenUrl` fonction pour transmettre l’URL à la bibliothèque MSAL. Ajoutez les éléments suivants à `AppDelegate` la classe.

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial.iOS/AppDelegate.cs" id="OpenUrlSnippet":::

## <a name="storing-the-tokens"></a>Stockage des jetons

Lorsque la bibliothèque d’authentification Microsoft est utilisée dans un projet Xamarin, elle tire parti du stockage sécurisé natif pour mettre en cache les jetons par défaut. Pour plus d’informations, consultez [la rubrique sérialisation du cache de jetons](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization) .

## <a name="test-sign-in"></a>Tester la connexion

À ce stade, si vous exécutez l’application et que vous appuyez sur le bouton **de connexion** , vous êtes invité à vous connecter. Lors de la connexion, le jeton d’accès doit s’afficher dans la sortie du débogueur.

![Capture d’écran de la fenêtre sortie dans Visual Studio](./images/debugger-access-token.png)

## <a name="get-user-details"></a>Obtenir les détails de l’utilisateur

1. Ajoutez une nouvelle fonction à la classe **app** pour initialiser le `GraphServiceClient`.

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="InitializeGraphClientSnippet":::

1. Mettez à `SignIn` jour la fonction dans **app.Xaml.cs** pour appeler cette fonction `GetUserInfo`au lieu de. Supprimez les éléments suivants `SignIn` de la fonction.

    ```csharp
    await GetUserInfo();

    IsSignedIn = true;
    ```

1. Ajoutez le code suivant à la fin de `SignIn` la fonction.

    ```csharp
    await InitializeGraphClientAsync();
    ```

1. Mettez à `GetUserInfo` jour la fonction pour obtenir les détails de l’utilisateur à partir de Microsoft Graph. Remplacez la fonction `GetUserInfo` existante par ce qui suit.

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="GetUserInfoSnippet":::

1. Enregistrez vos modifications et exécutez l’application. Après la connexion, l’interface utilisateur est mise à jour avec le nom d’affichage et l’adresse de messagerie de l’utilisateur.
