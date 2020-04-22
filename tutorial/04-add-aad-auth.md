<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="163f7-101">Dans cet exercice, vous allez étendre l’application de l’exercice précédent pour prendre en charge l’authentification avec Azure AD.</span><span class="sxs-lookup"><span data-stu-id="163f7-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="163f7-102">Cela est nécessaire pour obtenir le jeton d’accès OAuth nécessaire pour appeler Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="163f7-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="163f7-103">Dans cette étape, vous allez intégrer la [bibliothèque d’authentification Microsoft pour .net (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) dans l’application.</span><span class="sxs-lookup"><span data-stu-id="163f7-103">In this step you will integrate the [Microsoft Authentication Library for .NET (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) into the application.</span></span>

1. <span data-ttu-id="163f7-104">Dans **l’Explorateur de solutions**, développez le projet **GraphTutorial** , puis cliquez avec le bouton droit sur le dossier **modèles** .</span><span class="sxs-lookup"><span data-stu-id="163f7-104">In **Solution Explorer**, expand the **GraphTutorial** project and right-click the **Models** folder.</span></span> <span data-ttu-id="163f7-105">Sélectionnez **Ajouter une classe de >.**... Nommez la `OAuthSettings` classe et sélectionnez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="163f7-105">Select **Add > Class...**. Name the class `OAuthSettings` and select **Add**.</span></span>

1. <span data-ttu-id="163f7-106">Ouvrez le fichier **OAuthSettings.cs** et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="163f7-106">Open the **OAuthSettings.cs** file and replace its contents with the following.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/Models/OAuthSettings.cs.example":::

1. <span data-ttu-id="163f7-107">Remplacez `YOUR_APP_ID_HERE` par l’ID d’application de l’inscription de votre application.</span><span class="sxs-lookup"><span data-stu-id="163f7-107">Replace `YOUR_APP_ID_HERE` with the application ID from your app registration.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="163f7-108">Si vous utilisez le contrôle de code source tel que git, il est maintenant recommandé d’exclure le `OAuthSettings.cs` fichier du contrôle de code source afin d’éviter une fuite accidentelle de votre ID d’application.</span><span class="sxs-lookup"><span data-stu-id="163f7-108">If you're using source control such as git, now would be a good time to exclude the `OAuthSettings.cs` file from source control to avoid inadvertently leaking your app ID.</span></span>

## <a name="implement-sign-in"></a><span data-ttu-id="163f7-109">Implémentation de la connexion</span><span class="sxs-lookup"><span data-stu-id="163f7-109">Implement sign-in</span></span>

1. <span data-ttu-id="163f7-110">Ouvrez le fichier **app.Xaml.cs** dans le projet **GraphTutorial** et ajoutez les instructions suivantes `using` en haut du fichier.</span><span class="sxs-lookup"><span data-stu-id="163f7-110">Open the **App.xaml.cs** file in the **GraphTutorial** project, and add the following `using` statements to the top of the file.</span></span>

    ```csharp
    using GraphTutorial.Models;
    using Microsoft.Identity.Client;
    using Microsoft.Graph;
    using System.Diagnostics;
    using System.Linq;
    using System.Net.Http.Headers;
    ```

1. <span data-ttu-id="163f7-111">Modifiez la ligne de déclaration de classe d' **application** pour résoudre le conflit de nom pour l' **application**.</span><span class="sxs-lookup"><span data-stu-id="163f7-111">Change the **App** class declaration line to resolve the name conflict for **Application**.</span></span>

    ```csharp
    public partial class App : Xamarin.Forms.Application, INotifyPropertyChanged
    ```

1. <span data-ttu-id="163f7-112">Ajoutez les propriétés suivantes à la `App` classe.</span><span class="sxs-lookup"><span data-stu-id="163f7-112">Add the following properties to the `App` class.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="AuthPropertiesSnippet":::z

1. <span data-ttu-id="163f7-113">Ensuite, créez un nouveau `PublicClientApplication` dans le constructeur de la `App` classe.</span><span class="sxs-lookup"><span data-stu-id="163f7-113">Next, create a new `PublicClientApplication` in the constructor of the `App` class.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="AppConstructorSnippet" highlight="5-14":::

1. <span data-ttu-id="163f7-114">Mettez à `SignIn` jour la fonction pour `PublicClientApplication` utiliser l’pour obtenir un jeton d’accès.</span><span class="sxs-lookup"><span data-stu-id="163f7-114">Update the `SignIn` function to use the `PublicClientApplication` to get an access token.</span></span> <span data-ttu-id="163f7-115">Ajoutez le code suivant au- `await GetUserInfo();` dessus de la ligne.</span><span class="sxs-lookup"><span data-stu-id="163f7-115">Add the following code above the `await GetUserInfo();` line.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="GetTokenSnippet":::

    <span data-ttu-id="163f7-116">Ce code tente d’abord d’obtenir un jeton d’accès en mode silencieux.</span><span class="sxs-lookup"><span data-stu-id="163f7-116">This code first attempts to get an access token silently.</span></span> <span data-ttu-id="163f7-117">Si les informations d’un utilisateur figurent déjà dans le cache de l’application (par exemple, si l’utilisateur a fermé l’application précédemment sans se déconnecter), cette opération réussit et il n’y a aucune raison d’inviter l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="163f7-117">If a user's information is already in the app's cache (for example, if the user closed the app previously without signing out), this will succeed, and there's no reason to prompt the user.</span></span> <span data-ttu-id="163f7-118">S’il n’y a pas d’informations d’utilisateur dans le cache `AcquireTokenSilent().ExecuteAsync()` , la fonction génère `MsalUiRequiredException`un.</span><span class="sxs-lookup"><span data-stu-id="163f7-118">If there is not a user's information in the cache, the `AcquireTokenSilent().ExecuteAsync()` function throws an `MsalUiRequiredException`.</span></span> <span data-ttu-id="163f7-119">Dans ce cas, le code appelle la fonction interactive pour obtenir un jeton, `AcquireTokenInteractive`.</span><span class="sxs-lookup"><span data-stu-id="163f7-119">In this case, the code calls the interactive function to get a token, `AcquireTokenInteractive`.</span></span>

1. <span data-ttu-id="163f7-120">Mettez à `SignOut` jour la fonction pour supprimer les informations de l’utilisateur du cache.</span><span class="sxs-lookup"><span data-stu-id="163f7-120">Update the `SignOut` function to remove the user's information from the cache.</span></span> <span data-ttu-id="163f7-121">Ajoutez le code suivant au début de la `SignOut` fonction.</span><span class="sxs-lookup"><span data-stu-id="163f7-121">Add the following code to the beginning of the `SignOut` function.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="RemoveAccountSnippet":::

### <a name="update-android-project-to-enable-sign-in"></a><span data-ttu-id="163f7-122">Mettre à jour le projet Android pour activer la connexion</span><span class="sxs-lookup"><span data-stu-id="163f7-122">Update Android project to enable sign-in</span></span>

<span data-ttu-id="163f7-123">Lorsqu’elle est utilisée dans un projet Android Xamarin, la bibliothèque d’authentification Microsoft a quelques [exigences spécifiques à Android](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-Android-specifics).</span><span class="sxs-lookup"><span data-stu-id="163f7-123">When used in a Xamarin Android project, the Microsoft Authentication Library has a few [requirements specific to Android](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-Android-specifics).</span></span>

1. <span data-ttu-id="163f7-124">Dans le projet **GraphTutorial. Android** , développez le dossier **Propriétés** , puis ouvrez **AndroidManifest. xml**.</span><span class="sxs-lookup"><span data-stu-id="163f7-124">In **GraphTutorial.Android** project, expand the **Properties** folder, then open **AndroidManifest.xml**.</span></span> <span data-ttu-id="163f7-125">Si vous utilisez Visual Studio pour Mac, Control Click **AndroidManifest. xml** et choisissez **Ouvrir avec**, puis **éditeur de code source**.</span><span class="sxs-lookup"><span data-stu-id="163f7-125">If you're using Visual Studio for Mac, Control click **AndroidManifest.xml** and choose **Open With**, then **Source Code Editor**.</span></span> <span data-ttu-id="163f7-126">Remplacez tout le contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="163f7-126">Replace the entire contents with the following.</span></span>

    :::code language="xml" source="../demo/GraphTutorial/GraphTutorial.Android/Properties/AndroidManifest.xml":::

1. <span data-ttu-id="163f7-127">Ouvrez **MainActivity.cs** et ajoutez les instructions `using` suivantes en haut du fichier.</span><span class="sxs-lookup"><span data-stu-id="163f7-127">Open **MainActivity.cs** and add the following `using` statements to the top of the file.</span></span>

    ```csharp
    using Android.Content;
    using Microsoft.Identity.Client;
    ```

1. <span data-ttu-id="163f7-128">Remplacez la `OnActivityResult` fonction pour transmettre le contrôle à la bibliothèque MSAL.</span><span class="sxs-lookup"><span data-stu-id="163f7-128">Override the `OnActivityResult` function to pass control to the MSAL library.</span></span> <span data-ttu-id="163f7-129">Ajoutez les éléments suivants à `MainActivity` la classe.</span><span class="sxs-lookup"><span data-stu-id="163f7-129">Add the following to the `MainActivity` class.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial.Android/MainActivity.cs" id="OnActivityResultSnippet":::

1. <span data-ttu-id="163f7-130">Dans la `OnCreate` fonction, ajoutez la ligne suivante après la `LoadApplication(new App());` ligne.</span><span class="sxs-lookup"><span data-stu-id="163f7-130">In the `OnCreate` function, add the following line after the `LoadApplication(new App());` line.</span></span>

    ```csharp
    App.AuthUIParent = this;
    ```

### <a name="update-ios-project-to-enable-sign-in"></a><span data-ttu-id="163f7-131">Mettre à jour le projet iOS pour activer la connexion</span><span class="sxs-lookup"><span data-stu-id="163f7-131">Update iOS project to enable sign-in</span></span>

> [!IMPORTANT]
> <span data-ttu-id="163f7-132">Étant donné que MSAL nécessite l’utilisation d’un fichier habilitations. plist, vous devez configurer Visual Studio avec votre compte de développeur Apple pour activer la mise en service.</span><span class="sxs-lookup"><span data-stu-id="163f7-132">Because MSAL requires use of an Entitlements.plist file, you must configure Visual Studio with your Apple developer account to enable provisioning.</span></span> <span data-ttu-id="163f7-133">Si vous exécutez ce didacticiel dans le simulateur iPhone, vous devez ajouter les **habilitations. plist** dans le champ **habilitations personnalisées** dans les paramètres du projet **GraphTutorial. iOS** , **signature du package de >iOS**.</span><span class="sxs-lookup"><span data-stu-id="163f7-133">If you are running this tutorial in the iPhone simulator, you need to add **Entitlements.plist** in the **Custom Entitlements** field in **GraphTutorial.iOS** project's settings, **Build->iOS Bundle Signing**.</span></span> <span data-ttu-id="163f7-134">Pour plus d’informations, consultez la rubrique [mise en service de l’appareil pour Xamarin. iOS](/xamarin/ios/get-started/installation/device-provisioning).</span><span class="sxs-lookup"><span data-stu-id="163f7-134">For more information, see [Device provisioning for Xamarin.iOS](/xamarin/ios/get-started/installation/device-provisioning).</span></span>

<span data-ttu-id="163f7-135">Lorsqu’elle est utilisée dans un projet iOS Xamarin, la bibliothèque d’authentification Microsoft a quelques [exigences spécifiques à IOS](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-iOS-specifics).</span><span class="sxs-lookup"><span data-stu-id="163f7-135">When used in a Xamarin iOS project, the Microsoft Authentication Library has a few [requirements specific to iOS](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-iOS-specifics).</span></span>

1. <span data-ttu-id="163f7-136">Dans l’Explorateur de solutions, développez le projet **GraphTutorial. iOS** , puis ouvrez le fichier **habilitations. plist** .</span><span class="sxs-lookup"><span data-stu-id="163f7-136">In Solution Explorer, expand the **GraphTutorial.iOS** project, then open the **Entitlements.plist** file.</span></span>

1. <span data-ttu-id="163f7-137">Localisez le **trousseau** d’autorisations, puis sélectionnez **activer la chaîne de trousseau**.</span><span class="sxs-lookup"><span data-stu-id="163f7-137">Locate the **Keychain** entitlement, and select **Enable KeyChain**.</span></span>

1. <span data-ttu-id="163f7-138">Dans les **groupes de trousseau**, ajoutez une entrée au format `com.companyname.GraphTutorial`.</span><span class="sxs-lookup"><span data-stu-id="163f7-138">In **Keychain Groups**, add an entry with the format `com.companyname.GraphTutorial`.</span></span>

    ![Capture d’écran de la configuration de la habilitation de trousseau](./images/enable-keychain-access.png)

1. <span data-ttu-id="163f7-140">Mettez à jour le code dans le projet **GraphTutorial. iOS** afin de gérer la redirection lors de l’authentification.</span><span class="sxs-lookup"><span data-stu-id="163f7-140">Update the code in the **GraphTutorial.iOS** project to handle the redirect during authentication.</span></span> <span data-ttu-id="163f7-141">Ouvrez le fichier **AppDelegate.cs** et ajoutez l’instruction `using` suivante en haut du fichier.</span><span class="sxs-lookup"><span data-stu-id="163f7-141">Open the **AppDelegate.cs** file and add the following `using` statement at the top of the file.</span></span>

    ```csharp
    using Microsoft.Identity.Client;
    ```

1. <span data-ttu-id="163f7-142">Ajoutez la ligne suivante pour `FinishedLaunching` fonctionner juste avant la `LoadApplication(new App());` ligne.</span><span class="sxs-lookup"><span data-stu-id="163f7-142">Add the following line to `FinishedLaunching` function just before the `LoadApplication(new App());` line.</span></span>

    ```csharp
    // Specify the Keychain access group
    App.iOSKeychainSecurityGroup = NSBundle.MainBundle.BundleIdentifier;
    ```

1. <span data-ttu-id="163f7-143">Remplacez la `OpenUrl` fonction pour transmettre l’URL à la bibliothèque MSAL.</span><span class="sxs-lookup"><span data-stu-id="163f7-143">Override the `OpenUrl` function to pass the URL to the MSAL library.</span></span> <span data-ttu-id="163f7-144">Ajoutez les éléments suivants à `AppDelegate` la classe.</span><span class="sxs-lookup"><span data-stu-id="163f7-144">Add the following to the `AppDelegate` class.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial.iOS/AppDelegate.cs" id="OpenUrlSnippet":::

## <a name="storing-the-tokens"></a><span data-ttu-id="163f7-145">Stockage des jetons</span><span class="sxs-lookup"><span data-stu-id="163f7-145">Storing the tokens</span></span>

<span data-ttu-id="163f7-146">Lorsque la bibliothèque d’authentification Microsoft est utilisée dans un projet Xamarin, elle tire parti du stockage sécurisé natif pour mettre en cache les jetons par défaut.</span><span class="sxs-lookup"><span data-stu-id="163f7-146">When the Microsoft Authentication Library is used in a Xamarin project, it takes advantage of the native secure storage to cache tokens by default.</span></span> <span data-ttu-id="163f7-147">Pour plus d’informations, consultez [la rubrique sérialisation du cache de jetons](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization) .</span><span class="sxs-lookup"><span data-stu-id="163f7-147">See [Token cache serialization](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization) for more information.</span></span>

## <a name="test-sign-in"></a><span data-ttu-id="163f7-148">Tester la connexion</span><span class="sxs-lookup"><span data-stu-id="163f7-148">Test sign-in</span></span>

<span data-ttu-id="163f7-149">À ce stade, si vous exécutez l’application et que vous appuyez sur le bouton **de connexion** , vous êtes invité à vous connecter.</span><span class="sxs-lookup"><span data-stu-id="163f7-149">At this point if you run the application and tap the **Sign in** button, you are prompted to sign in.</span></span> <span data-ttu-id="163f7-150">Lors de la connexion, le jeton d’accès doit s’afficher dans la sortie du débogueur.</span><span class="sxs-lookup"><span data-stu-id="163f7-150">On successful sign in, you should see the access token printed into the debugger's output.</span></span>

![Capture d’écran de la fenêtre sortie dans Visual Studio](./images/debugger-access-token.png)

## <a name="get-user-details"></a><span data-ttu-id="163f7-152">Obtenir les détails de l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="163f7-152">Get user details</span></span>

1. <span data-ttu-id="163f7-153">Ajoutez une nouvelle fonction à la classe **app** pour initialiser le `GraphServiceClient`.</span><span class="sxs-lookup"><span data-stu-id="163f7-153">Add a new function to the **App** class to initialize the `GraphServiceClient`.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="InitializeGraphClientSnippet":::

1. <span data-ttu-id="163f7-154">Mettez à `SignIn` jour la fonction dans **app.Xaml.cs** pour appeler cette fonction `GetUserInfo`au lieu de.</span><span class="sxs-lookup"><span data-stu-id="163f7-154">Update the `SignIn` function in **App.xaml.cs** to call this function instead of `GetUserInfo`.</span></span> <span data-ttu-id="163f7-155">Supprimez les éléments suivants `SignIn` de la fonction.</span><span class="sxs-lookup"><span data-stu-id="163f7-155">Remove the following from the `SignIn` function.</span></span>

    ```csharp
    await GetUserInfo();

    IsSignedIn = true;
    ```

1. <span data-ttu-id="163f7-156">Ajoutez le code suivant à la fin de `SignIn` la fonction.</span><span class="sxs-lookup"><span data-stu-id="163f7-156">Add the following to the end of the `SignIn` function.</span></span>

    ```csharp
    await InitializeGraphClientAsync();
    ```

1. <span data-ttu-id="163f7-157">Mettez à `GetUserInfo` jour la fonction pour obtenir les détails de l’utilisateur à partir de Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="163f7-157">Update the `GetUserInfo` function to get the user's details from the Microsoft Graph.</span></span> <span data-ttu-id="163f7-158">Remplacez la fonction `GetUserInfo` existante par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="163f7-158">Replace the existing `GetUserInfo` function with the following.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="GetUserInfoSnippet":::

1. <span data-ttu-id="163f7-159">Enregistrez vos modifications et exécutez l’application.</span><span class="sxs-lookup"><span data-stu-id="163f7-159">Save your changes and run the app.</span></span> <span data-ttu-id="163f7-160">Après la connexion, l’interface utilisateur est mise à jour avec le nom d’affichage et l’adresse de messagerie de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="163f7-160">After sign-in the UI is updated with the user's display name and email address.</span></span>
