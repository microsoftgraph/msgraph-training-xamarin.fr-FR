<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="23329-101">Dans cet exercice, vous allez étendre l’application de l’exercice précédent pour prendre en charge l’authentification avec Azure AD.</span><span class="sxs-lookup"><span data-stu-id="23329-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="23329-102">Cette étape est nécessaire pour obtenir le jeton d’accès OAuth nécessaire pour appeler l’Graph Microsoft.</span><span class="sxs-lookup"><span data-stu-id="23329-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="23329-103">Dans cette étape, vous allez intégrer la bibliothèque d’authentification [Microsoft pour .NET (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) dans l’application.</span><span class="sxs-lookup"><span data-stu-id="23329-103">In this step you will integrate the [Microsoft Authentication Library for .NET (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) into the application.</span></span>

1. <span data-ttu-id="23329-104">Dans **l’Explorateur de** solutions, développez **le projet GraphTutorial** et cliquez avec le bouton droit sur **le dossier Modèles.**</span><span class="sxs-lookup"><span data-stu-id="23329-104">In **Solution Explorer**, expand the **GraphTutorial** project and right-click the **Models** folder.</span></span> <span data-ttu-id="23329-105">Sélectionnez **Ajouter > classe...**. Nommez la classe `OAuthSettings` et sélectionnez **Ajouter.**</span><span class="sxs-lookup"><span data-stu-id="23329-105">Select **Add > Class...**. Name the class `OAuthSettings` and select **Add**.</span></span>

1. <span data-ttu-id="23329-106">Ouvrez **le fichier OAuthSettings.cs** et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="23329-106">Open the **OAuthSettings.cs** file and replace its contents with the following.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/Models/OAuthSettings.example.cs":::

1. <span data-ttu-id="23329-107">Remplacez `YOUR_APP_ID_HERE` par l’ID d’application de l’inscription de votre application.</span><span class="sxs-lookup"><span data-stu-id="23329-107">Replace `YOUR_APP_ID_HERE` with the application ID from your app registration.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="23329-108">Si vous utilisez un contrôle source tel que Git, il est temps d’exclure le fichier du contrôle source afin d’éviter toute fuite accidentelle de votre `OAuthSettings.cs` ID d’application.</span><span class="sxs-lookup"><span data-stu-id="23329-108">If you're using source control such as git, now would be a good time to exclude the `OAuthSettings.cs` file from source control to avoid inadvertently leaking your app ID.</span></span>

## <a name="implement-sign-in"></a><span data-ttu-id="23329-109">Implémentation de la connexion</span><span class="sxs-lookup"><span data-stu-id="23329-109">Implement sign-in</span></span>

1. <span data-ttu-id="23329-110">Ouvrez **le fichier App.xaml.cs** dans le projet **GraphTutorial** et ajoutez les instructions suivantes en haut `using` du fichier.</span><span class="sxs-lookup"><span data-stu-id="23329-110">Open the **App.xaml.cs** file in the **GraphTutorial** project, and add the following `using` statements to the top of the file.</span></span>

    ```csharp
    using GraphTutorial.Models;
    using Microsoft.Identity.Client;
    using Microsoft.Graph;
    using System.Diagnostics;
    using System.Linq;
    using System.Net.Http.Headers;
    using TimeZoneConverter;
    ```

1. <span data-ttu-id="23329-111">Modifiez la **ligne de déclaration** de classe d’application pour résoudre le conflit de noms pour **Application**.</span><span class="sxs-lookup"><span data-stu-id="23329-111">Change the **App** class declaration line to resolve the name conflict for **Application**.</span></span>

    ```csharp
    public partial class App : Xamarin.Forms.Application, INotifyPropertyChanged
    ```

1. <span data-ttu-id="23329-112">Ajoutez les propriétés suivantes à la `App` classe.</span><span class="sxs-lookup"><span data-stu-id="23329-112">Add the following properties to the `App` class.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="AuthPropertiesSnippet":::z

1. <span data-ttu-id="23329-113">Ensuite, créez une `PublicClientApplication` nouvelle classe dans le constructeur de la `App` classe.</span><span class="sxs-lookup"><span data-stu-id="23329-113">Next, create a new `PublicClientApplication` in the constructor of the `App` class.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="AppConstructorSnippet" highlight="5-14":::

1. <span data-ttu-id="23329-114">Mettez à jour `SignIn` la fonction pour utiliser la fonction pour obtenir un `PublicClientApplication` jeton d’accès.</span><span class="sxs-lookup"><span data-stu-id="23329-114">Update the `SignIn` function to use the `PublicClientApplication` to get an access token.</span></span> <span data-ttu-id="23329-115">Ajoutez le code suivant au-dessus de la `await GetUserInfo();` ligne.</span><span class="sxs-lookup"><span data-stu-id="23329-115">Add the following code above the `await GetUserInfo();` line.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="GetTokenSnippet":::

    <span data-ttu-id="23329-116">Ce code tente d’abord d’obtenir un jeton d’accès en mode silencieux.</span><span class="sxs-lookup"><span data-stu-id="23329-116">This code first attempts to get an access token silently.</span></span> <span data-ttu-id="23329-117">Si les informations d’un utilisateur se trouve déjà dans le cache de l’application (par exemple, si l’utilisateur a fermé l’application précédemment sans se désener), cela réussit et il n’y a aucune raison d’en informer l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="23329-117">If a user's information is already in the app's cache (for example, if the user closed the app previously without signing out), this will succeed, and there's no reason to prompt the user.</span></span> <span data-ttu-id="23329-118">S’il n’y a pas d’informations d’utilisateur dans le cache, la `AcquireTokenSilent().ExecuteAsync()` fonction envoie un `MsalUiRequiredException` .</span><span class="sxs-lookup"><span data-stu-id="23329-118">If there is not a user's information in the cache, the `AcquireTokenSilent().ExecuteAsync()` function throws an `MsalUiRequiredException`.</span></span> <span data-ttu-id="23329-119">Dans ce cas, le code appelle la fonction interactive pour obtenir un `AcquireTokenInteractive` jeton.</span><span class="sxs-lookup"><span data-stu-id="23329-119">In this case, the code calls the interactive function to get a token, `AcquireTokenInteractive`.</span></span>

1. <span data-ttu-id="23329-120">Mettez à `SignOut` jour la fonction pour supprimer les informations de l’utilisateur du cache.</span><span class="sxs-lookup"><span data-stu-id="23329-120">Update the `SignOut` function to remove the user's information from the cache.</span></span> <span data-ttu-id="23329-121">Ajoutez le code suivant au début de la `SignOut` fonction.</span><span class="sxs-lookup"><span data-stu-id="23329-121">Add the following code to the beginning of the `SignOut` function.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="RemoveAccountSnippet":::

### <a name="update-android-project-to-enable-sign-in"></a><span data-ttu-id="23329-122">Mettre à jour le projet Android pour activer la sign-in</span><span class="sxs-lookup"><span data-stu-id="23329-122">Update Android project to enable sign-in</span></span>

<span data-ttu-id="23329-123">Lorsqu’elle est utilisée dans un projet Xamarin Android, la bibliothèque d’authentification Microsoft présente quelques exigences [spécifiques à Android.](/azure/active-directory/develop/msal-net-xamarin-android-considerations)</span><span class="sxs-lookup"><span data-stu-id="23329-123">When used in a Xamarin Android project, the Microsoft Authentication Library has a few [requirements specific to Android](/azure/active-directory/develop/msal-net-xamarin-android-considerations).</span></span>

1. <span data-ttu-id="23329-124">Dans **le projet GraphTutorial.Android,** développez le dossier **Propriétés,** puis **ouvrezAndroidManifest.xml**.</span><span class="sxs-lookup"><span data-stu-id="23329-124">In **GraphTutorial.Android** project, expand the **Properties** folder, then open **AndroidManifest.xml**.</span></span> <span data-ttu-id="23329-125">Si vous utilisez Visual Studio pour Mac, cliquez surAndroidManifest.xml **puis** choisissez **Ouvrir** avec, puis Éditeur de **code source.**</span><span class="sxs-lookup"><span data-stu-id="23329-125">If you're using Visual Studio for Mac, Control click **AndroidManifest.xml** and choose **Open With**, then **Source Code Editor**.</span></span> <span data-ttu-id="23329-126">Remplacez tout le contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="23329-126">Replace the entire contents with the following.</span></span>

    :::code language="xml" source="../demo/GraphTutorial/GraphTutorial.Android/Properties/AndroidManifest.xml":::

1. <span data-ttu-id="23329-127">Ouvrez **MainActivity.cs** et ajoutez les `using` instructions suivantes en haut du fichier.</span><span class="sxs-lookup"><span data-stu-id="23329-127">Open **MainActivity.cs** and add the following `using` statements to the top of the file.</span></span>

    ```csharp
    using Android.Content;
    using Microsoft.Identity.Client;
    ```

1. <span data-ttu-id="23329-128">Remplacez la `OnActivityResult` fonction pour passer le contrôle à la bibliothèque MSAL.</span><span class="sxs-lookup"><span data-stu-id="23329-128">Override the `OnActivityResult` function to pass control to the MSAL library.</span></span> <span data-ttu-id="23329-129">Ajoutez ce qui suit à la `MainActivity` classe.</span><span class="sxs-lookup"><span data-stu-id="23329-129">Add the following to the `MainActivity` class.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial.Android/MainActivity.cs" id="OnActivityResultSnippet":::

1. <span data-ttu-id="23329-130">Dans la `OnCreate` fonction, ajoutez la ligne suivante après la `LoadApplication(new App());` ligne.</span><span class="sxs-lookup"><span data-stu-id="23329-130">In the `OnCreate` function, add the following line after the `LoadApplication(new App());` line.</span></span>

    ```csharp
    App.AuthUIParent = this;
    ```

### <a name="update-ios-project-to-enable-sign-in"></a><span data-ttu-id="23329-131">Mettre à jour le projet iOS pour activer la connectez-vous</span><span class="sxs-lookup"><span data-stu-id="23329-131">Update iOS project to enable sign-in</span></span>

> [!IMPORTANT]
> <span data-ttu-id="23329-132">Étant donné que MSAL nécessite l’utilisation d’un fichier Entitlements.plist, vous devez configurer Visual Studio avec votre compte de développeur Apple pour activer l’approvisionnement.</span><span class="sxs-lookup"><span data-stu-id="23329-132">Because MSAL requires use of an Entitlements.plist file, you must configure Visual Studio with your Apple developer account to enable provisioning.</span></span> <span data-ttu-id="23329-133">Si vous exécutez ce didacticiel dans le simulateur iPhone, vous devez ajouter **Entitlements.plist** dans le champ Droits personnalisés dans les **paramètres** du projet **GraphTutorial.iOS,** **Build->iOS Bundle Signing**.</span><span class="sxs-lookup"><span data-stu-id="23329-133">If you are running this tutorial in the iPhone simulator, you need to add **Entitlements.plist** in the **Custom Entitlements** field in **GraphTutorial.iOS** project's settings, **Build->iOS Bundle Signing**.</span></span> <span data-ttu-id="23329-134">Pour plus d’informations, [voir Device provisioning for Xamarin.iOS](/xamarin/ios/get-started/installation/device-provisioning).</span><span class="sxs-lookup"><span data-stu-id="23329-134">For more information, see [Device provisioning for Xamarin.iOS](/xamarin/ios/get-started/installation/device-provisioning).</span></span>

<span data-ttu-id="23329-135">Lorsqu’elle est utilisée dans un projet IOS Xamarin, la bibliothèque d’authentification Microsoft a quelques exigences [spécifiques à iOS.](/azure/active-directory/develop/msal-net-xamarin-ios-considerations)</span><span class="sxs-lookup"><span data-stu-id="23329-135">When used in a Xamarin iOS project, the Microsoft Authentication Library has a few [requirements specific to iOS](/azure/active-directory/develop/msal-net-xamarin-ios-considerations).</span></span>

1. <span data-ttu-id="23329-136">Dans l’Explorateur de solutions, développez **le projet GraphTutorial.iOS,** puis ouvrez le fichier **Entitlements.plist.**</span><span class="sxs-lookup"><span data-stu-id="23329-136">In Solution Explorer, expand the **GraphTutorial.iOS** project, then open the **Entitlements.plist** file.</span></span>

1. <span data-ttu-id="23329-137">Recherchez **le droit auchain,** puis **sélectionnez Activer lechain.**</span><span class="sxs-lookup"><span data-stu-id="23329-137">Locate the **Keychain** entitlement, and select **Enable KeyChain**.</span></span>

1. <span data-ttu-id="23329-138">Dans **les groupes dechains,** ajoutez une entrée au format `com.companyname.GraphTutorial` .</span><span class="sxs-lookup"><span data-stu-id="23329-138">In **Keychain Groups**, add an entry with the format `com.companyname.GraphTutorial`.</span></span>

    ![Capture d’écran de la configuration des droits duchain](./images/enable-keychain-access.png)

1. <span data-ttu-id="23329-140">Mettez à jour le code dans **le projet GraphTutorial.iOS** pour gérer la redirection pendant l’authentification.</span><span class="sxs-lookup"><span data-stu-id="23329-140">Update the code in the **GraphTutorial.iOS** project to handle the redirect during authentication.</span></span> <span data-ttu-id="23329-141">Ouvrez **le fichier AppDelegate.cs** et ajoutez l’instruction `using` suivante en haut du fichier.</span><span class="sxs-lookup"><span data-stu-id="23329-141">Open the **AppDelegate.cs** file and add the following `using` statement at the top of the file.</span></span>

    ```csharp
    using Microsoft.Identity.Client;
    ```

1. <span data-ttu-id="23329-142">Ajoutez la ligne suivante pour `FinishedLaunching` qu’elle fonctionne juste avant la `LoadApplication(new App());` ligne.</span><span class="sxs-lookup"><span data-stu-id="23329-142">Add the following line to `FinishedLaunching` function just before the `LoadApplication(new App());` line.</span></span>

    ```csharp
    // Specify the Keychain access group
    App.iOSKeychainSecurityGroup = NSBundle.MainBundle.BundleIdentifier;
    ```

1. <span data-ttu-id="23329-143">Remplacez la `OpenUrl` fonction pour transmettre l’URL à la bibliothèque MSAL.</span><span class="sxs-lookup"><span data-stu-id="23329-143">Override the `OpenUrl` function to pass the URL to the MSAL library.</span></span> <span data-ttu-id="23329-144">Ajoutez ce qui suit à la `AppDelegate` classe.</span><span class="sxs-lookup"><span data-stu-id="23329-144">Add the following to the `AppDelegate` class.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial.iOS/AppDelegate.cs" id="OpenUrlSnippet":::

## <a name="storing-the-tokens"></a><span data-ttu-id="23329-145">Stockage des jetons</span><span class="sxs-lookup"><span data-stu-id="23329-145">Storing the tokens</span></span>

<span data-ttu-id="23329-146">Lorsque la bibliothèque d’authentification Microsoft est utilisée dans un projet Xamarin, elle tire parti du stockage sécurisé natif pour mettre en cache les jetons par défaut.</span><span class="sxs-lookup"><span data-stu-id="23329-146">When the Microsoft Authentication Library is used in a Xamarin project, it takes advantage of the native secure storage to cache tokens by default.</span></span> <span data-ttu-id="23329-147">Pour plus [d’informations, voir sérialisation](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization) du cache de jetons.</span><span class="sxs-lookup"><span data-stu-id="23329-147">See [Token cache serialization](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization) for more information.</span></span>

## <a name="test-sign-in"></a><span data-ttu-id="23329-148">Tester la sign-in</span><span class="sxs-lookup"><span data-stu-id="23329-148">Test sign-in</span></span>

<span data-ttu-id="23329-149">À ce stade, si vous  exécutez l’application et appuyez sur le bouton Se connectez, vous êtes invité à vous y inscrire.</span><span class="sxs-lookup"><span data-stu-id="23329-149">At this point if you run the application and tap the **Sign in** button, you are prompted to sign in.</span></span> <span data-ttu-id="23329-150">Si vous vous connectez avec succès, vous devez voir le jeton d’accès imprimé dans la sortie du débogger.</span><span class="sxs-lookup"><span data-stu-id="23329-150">On successful sign in, you should see the access token printed into the debugger's output.</span></span>

![Capture d’écran de la fenêtre Sortie dans Visual Studio](./images/debugger-access-token.png)

## <a name="get-user-details"></a><span data-ttu-id="23329-152">Obtenir les détails de l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="23329-152">Get user details</span></span>

1. <span data-ttu-id="23329-153">Ajoutez une nouvelle fonction à la classe **App** pour initialiser le `GraphServiceClient` .</span><span class="sxs-lookup"><span data-stu-id="23329-153">Add a new function to the **App** class to initialize the `GraphServiceClient`.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="InitializeGraphClientSnippet":::

1. <span data-ttu-id="23329-154">Mettez à `SignIn` jour la fonction dans **App.xaml.cs** pour appeler cette fonction au lieu de `GetUserInfo` .</span><span class="sxs-lookup"><span data-stu-id="23329-154">Update the `SignIn` function in **App.xaml.cs** to call this function instead of `GetUserInfo`.</span></span> <span data-ttu-id="23329-155">Supprimez ce qui suit de la `SignIn` fonction.</span><span class="sxs-lookup"><span data-stu-id="23329-155">Remove the following from the `SignIn` function.</span></span>

    ```csharp
    await GetUserInfo();

    IsSignedIn = true;
    ```

1. <span data-ttu-id="23329-156">Ajoutez ce qui suit à la fin de la `SignIn` fonction.</span><span class="sxs-lookup"><span data-stu-id="23329-156">Add the following to the end of the `SignIn` function.</span></span>

    ```csharp
    await InitializeGraphClientAsync();
    ```

1. <span data-ttu-id="23329-157">Mettez à `GetUserInfo` jour la fonction pour obtenir les détails de l’utilisateur à partir du Graph Microsoft.</span><span class="sxs-lookup"><span data-stu-id="23329-157">Update the `GetUserInfo` function to get the user's details from the Microsoft Graph.</span></span> <span data-ttu-id="23329-158">Remplacez la fonction `GetUserInfo` existante par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="23329-158">Replace the existing `GetUserInfo` function with the following.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="GetUserInfoSnippet":::

1. <span data-ttu-id="23329-159">Enregistrez vos modifications et exécutez l’application.</span><span class="sxs-lookup"><span data-stu-id="23329-159">Save your changes and run the app.</span></span> <span data-ttu-id="23329-160">Une fois que l’interface utilisateur est mise à jour avec le nom d’affichage et l’adresse e-mail de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="23329-160">After sign-in the UI is updated with the user's display name and email address.</span></span>
