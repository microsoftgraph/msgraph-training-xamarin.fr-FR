<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="8ec39-101">Dans cet exercice, vous allez étendre l’application de l’exercice précédent pour prendre en charge l’authentification avec Azure AD.</span><span class="sxs-lookup"><span data-stu-id="8ec39-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="8ec39-102">Cela est nécessaire pour obtenir le jeton d’accès OAuth nécessaire pour appeler Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="8ec39-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="8ec39-103">Dans cette étape, vous allez intégrer la [bibliothèque d’authentification Microsoft pour .net (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) dans l’application.</span><span class="sxs-lookup"><span data-stu-id="8ec39-103">In this step you will integrate the [Microsoft Authentication Library for .NET (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) into the application.</span></span>

<span data-ttu-id="8ec39-104">Dans **l’Explorateur de solutions**, développez le projet **GraphTutorial** , puis cliquez avec le bouton droit sur le dossier **modèles** .</span><span class="sxs-lookup"><span data-stu-id="8ec39-104">In **Solution Explorer**, expand the **GraphTutorial** project and right-click the **Models** folder.</span></span> <span data-ttu-id="8ec39-105">Sélectionnez **Ajouter une classe de >.**... Nommez la `OAuthSettings` classe et sélectionnez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="8ec39-105">Select **Add > Class...**. Name the class `OAuthSettings` and select **Add**.</span></span> <span data-ttu-id="8ec39-106">Ouvrez le fichier **OAuthSettings.cs** et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="8ec39-106">Open the **OAuthSettings.cs** file and replace its contents with the following.</span></span>

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

<span data-ttu-id="8ec39-107">Remplacez `YOUR_APP_ID_HERE` par l’ID d’application de l’inscription de votre application.</span><span class="sxs-lookup"><span data-stu-id="8ec39-107">Replace `YOUR_APP_ID_HERE` with the application ID from your app registration.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="8ec39-108">Si vous utilisez le contrôle de code source tel que git, il est maintenant recommandé d’exclure le `OAuthSettings.cs` fichier du contrôle de code source afin d’éviter une fuite accidentelle de votre ID d’application.</span><span class="sxs-lookup"><span data-stu-id="8ec39-108">If you're using source control such as git, now would be a good time to exclude the `OAuthSettings.cs` file from source control to avoid inadvertently leaking your app ID.</span></span>

## <a name="implement-sign-in"></a><span data-ttu-id="8ec39-109">Mettre en œuvre la connexion</span><span class="sxs-lookup"><span data-stu-id="8ec39-109">Implement sign-in</span></span>

<span data-ttu-id="8ec39-110">Ouvrez le fichier **app.Xaml.cs** dans le projet **GraphTutorial** et ajoutez les instructions suivantes `using` en haut du fichier.</span><span class="sxs-lookup"><span data-stu-id="8ec39-110">Open the **App.xaml.cs** file in the **GraphTutorial** project, and add the following `using` statements to the top of the file.</span></span>

```cs
using GraphTutorial.Models;
using Microsoft.Identity.Client;
using Microsoft.Graph;
using System.Diagnostics;
using System.Linq;
using System.Net.Http.Headers;
```

<span data-ttu-id="8ec39-111">Modifiez la ligne de déclaration de classe d' **application** pour résoudre le conflit de nom pour l' **application**.</span><span class="sxs-lookup"><span data-stu-id="8ec39-111">Change the **App** class declaration line to resolve the name conflict for **Application**.</span></span>

```cs
public partial class App : Xamarin.Forms.Application, INotifyPropertyChanged
```

<span data-ttu-id="8ec39-112">Ajoutez les propriétés suivantes à la `App` classe.</span><span class="sxs-lookup"><span data-stu-id="8ec39-112">Add the following properties to the `App` class.</span></span>

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

<span data-ttu-id="8ec39-113">Ensuite, créez un nouveau `PublicClientApplication` dans le constructeur de la `App` classe.</span><span class="sxs-lookup"><span data-stu-id="8ec39-113">Next, create a new `PublicClientApplication` in the constructor of the `App` class.</span></span>

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

<span data-ttu-id="8ec39-114">À présent, `SignIn` mettez à jour la `PublicClientApplication` fonction pour utiliser l’pour obtenir un jeton d’accès.</span><span class="sxs-lookup"><span data-stu-id="8ec39-114">Now update the `SignIn` function to use the `PublicClientApplication` to get an access token.</span></span> <span data-ttu-id="8ec39-115">Ajoutez le code suivant au- `await GetUserInfo();` dessus de la ligne.</span><span class="sxs-lookup"><span data-stu-id="8ec39-115">Add the following code above the `await GetUserInfo();` line.</span></span>

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

<span data-ttu-id="8ec39-116">Ce code tente d’abord d’obtenir un jeton d’accès en mode silencieux.</span><span class="sxs-lookup"><span data-stu-id="8ec39-116">This code first attempts to get an access token silently.</span></span> <span data-ttu-id="8ec39-117">Si les informations d’un utilisateur figurent déjà dans le cache de l’application (par exemple, si l’utilisateur a fermé l’application précédemment sans se déconnecter), cette opération réussit et il n’y a aucune raison d’inviter l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="8ec39-117">If a user's information is already in the app's cache (for example, if the user closed the app previously without signing out), this will succeed, and there's no reason to prompt the user.</span></span> <span data-ttu-id="8ec39-118">S’il n’y a pas d’informations d’utilisateur dans le cache `AcquireTokenSilent().ExecuteAsync()` , la fonction génère `MsalUiRequiredException`un.</span><span class="sxs-lookup"><span data-stu-id="8ec39-118">If there is not a user's information in the cache, the `AcquireTokenSilent().ExecuteAsync()` function throws an `MsalUiRequiredException`.</span></span> <span data-ttu-id="8ec39-119">Dans ce cas, le code appelle la fonction interactive pour obtenir un jeton, `AcquireTokenInteractive`.</span><span class="sxs-lookup"><span data-stu-id="8ec39-119">In this case, the code calls the interactive function to get a token, `AcquireTokenInteractive`.</span></span>

<span data-ttu-id="8ec39-120">À présent, `SignOut` mettez à jour la fonction pour supprimer les informations de l’utilisateur du cache.</span><span class="sxs-lookup"><span data-stu-id="8ec39-120">Now update the `SignOut` function to remove the user's information from the cache.</span></span> <span data-ttu-id="8ec39-121">Ajoutez le code suivant au début de la `SignOut` fonction.</span><span class="sxs-lookup"><span data-stu-id="8ec39-121">Add the following code to the beginning of the `SignOut` function.</span></span>

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

### <a name="update-android-project-to-enable-sign-in"></a><span data-ttu-id="8ec39-122">Mettre à jour le projet Android pour activer la connexion</span><span class="sxs-lookup"><span data-stu-id="8ec39-122">Update Android project to enable sign-in</span></span>

<span data-ttu-id="8ec39-123">Lorsqu’elle est utilisée dans un projet Android Xamarin, la bibliothèque d’authentification Microsoft a quelques [exigences spécifiques à Android](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-Android-specifics).</span><span class="sxs-lookup"><span data-stu-id="8ec39-123">When used in a Xamarin Android project, the Microsoft Authentication Library has a few [requirements specific to Android](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-Android-specifics).</span></span>

<span data-ttu-id="8ec39-124">Tout d’abord, vous devez mettre à jour le manifeste Android pour enregistrer l’URI de redirection.</span><span class="sxs-lookup"><span data-stu-id="8ec39-124">First, you need to update the Android manifest to register the redirect URI.</span></span> <span data-ttu-id="8ec39-125">Dans le projet **GraphTutorial. Android** , développez le dossier **Propriétés** , puis ouvrez **AndroidManifest. xml**.</span><span class="sxs-lookup"><span data-stu-id="8ec39-125">In **GraphTutorial.Android** project, expand the **Properties** folder, then open **AndroidManifest.xml**.</span></span> <span data-ttu-id="8ec39-126">Si vous utilisez Visual Studio pour Mac, basculez en mode **source** à l’aide des onglets situés en bas du fichier.</span><span class="sxs-lookup"><span data-stu-id="8ec39-126">If you're using Visual Studio for Mac, switch to the **Source** view using the tabs at the bottom of the file.</span></span> <span data-ttu-id="8ec39-127">Remplacez tout le contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="8ec39-127">Replace the entire contents with the following.</span></span>

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

<span data-ttu-id="8ec39-128">Remplacez `YOUR_APP_ID_HERE` par l’ID de votre application dans l’inscription de votre application.</span><span class="sxs-lookup"><span data-stu-id="8ec39-128">Replace `YOUR_APP_ID_HERE` with your application ID from your app registration.</span></span>

<span data-ttu-id="8ec39-129">Ensuite, ouvrez **MainActivity.cs** et ajoutez les instructions `using` suivantes en haut du fichier.</span><span class="sxs-lookup"><span data-stu-id="8ec39-129">Next, open **MainActivity.cs** and add the following `using` statements to the top of the file.</span></span>

```cs
using Microsoft.Identity.Client;
using Android.Content;
```

<span data-ttu-id="8ec39-130">Ensuite, substituez la `OnActivityResult` fonction pour transmettre le contrôle à la bibliothèque MSAL.</span><span class="sxs-lookup"><span data-stu-id="8ec39-130">Then, override the `OnActivityResult` function to pass control to the MSAL library.</span></span> <span data-ttu-id="8ec39-131">Ajoutez les éléments suivants à `MainActivity` la classe.</span><span class="sxs-lookup"><span data-stu-id="8ec39-131">Add the following to the `MainActivity` class.</span></span>

```cs
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);
    AuthenticationContinuationHelper
        .SetAuthenticationContinuationEventArgs(requestCode, resultCode, data);
}
```

<span data-ttu-id="8ec39-132">Enfin, dans la `OnCreate` fonction, ajoutez la ligne suivante après la `LoadApplication(new App());` ligne.</span><span class="sxs-lookup"><span data-stu-id="8ec39-132">Finally, in the `OnCreate` function, add the following line after the `LoadApplication(new App());` line.</span></span>

```cs
App.AuthUIParent = this;
```

### <a name="update-ios-project-to-enable-sign-in"></a><span data-ttu-id="8ec39-133">Mettre à jour le projet iOS pour activer la connexion</span><span class="sxs-lookup"><span data-stu-id="8ec39-133">Update iOS project to enable sign-in</span></span>

> [!IMPORTANT]
> <span data-ttu-id="8ec39-134">Étant donné que MSAL nécessite l’utilisation d’un fichier habilitations. plist, vous devez configurer Visual Studio avec votre compte de développeur Apple pour activer la mise en service.</span><span class="sxs-lookup"><span data-stu-id="8ec39-134">Because MSAL requires use of an Entitlements.plist file, you must configure Visual Studio with your Apple developer account to enable provisioning.</span></span> <span data-ttu-id="8ec39-135">Si vous exécutez ce didacticiel dans le simulateur iPhone, vous devez ajouter les **habilitations. plist** dans le champ **habilitations personnalisées** dans les paramètres du projet **GraphTutorial. iOS** , **signature du package de >iOS**.</span><span class="sxs-lookup"><span data-stu-id="8ec39-135">If you are running this tutorial in the iPhone simulator, you need to add **Entitlements.plist** in the **Custom Entitlements** field in **GraphTutorial.iOS** project's settings, **Build->iOS Bundle Signing**.</span></span> <span data-ttu-id="8ec39-136">Pour plus d’informations, consultez la rubrique [mise en service de l’appareil pour Xamarin. iOS](/xamarin/ios/get-started/installation/device-provisioning).</span><span class="sxs-lookup"><span data-stu-id="8ec39-136">For more information, see [Device provisioning for Xamarin.iOS](/xamarin/ios/get-started/installation/device-provisioning).</span></span>

<span data-ttu-id="8ec39-137">Lorsqu’elle est utilisée dans un projet iOS Xamarin, la bibliothèque d’authentification Microsoft a quelques [exigences spécifiques à IOS](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-iOS-specifics).</span><span class="sxs-lookup"><span data-stu-id="8ec39-137">When used in a Xamarin iOS project, the Microsoft Authentication Library has a few [requirements specific to iOS](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-iOS-specifics).</span></span>

<span data-ttu-id="8ec39-138">Tout d’abord, vous devez activer l’accès à la chaîne de trousseau.</span><span class="sxs-lookup"><span data-stu-id="8ec39-138">First, you need to enable Keychain access.</span></span> <span data-ttu-id="8ec39-139">Dans l’Explorateur de solutions, développez le projet **GraphTutorial. iOS** , puis ouvrez le fichier **habilitations. plist** .</span><span class="sxs-lookup"><span data-stu-id="8ec39-139">In Solution Explorer, expand the **GraphTutorial.iOS** project, then open the **Entitlements.plist** file.</span></span> <span data-ttu-id="8ec39-140">Localisez le **trousseau** d’autorisations, puis sélectionnez **activer la chaîne de trousseau**.</span><span class="sxs-lookup"><span data-stu-id="8ec39-140">Locate the **Keychain** entitlement, and select **Enable KeyChain**.</span></span> <span data-ttu-id="8ec39-141">Dans les **groupes de trousseau**, ajoutez une entrée au format `com.YOUR_DOMAIN.GraphTutorial`.</span><span class="sxs-lookup"><span data-stu-id="8ec39-141">In **Keychain Groups**, add an entry with the format `com.YOUR_DOMAIN.GraphTutorial`.</span></span>

![Capture d’écran de la configuration de la habilitation de trousseau](./images/enable-keychain-access.png)

<span data-ttu-id="8ec39-143">Ensuite, vous devez enregistrer l’URI de redirection MSAL par défaut en tant que type d’URL géré par votre application.</span><span class="sxs-lookup"><span data-stu-id="8ec39-143">Next, you need to register the default MSAL redirect URI as a URL type that your app handles.</span></span> <span data-ttu-id="8ec39-144">Ouvrez le fichier **info. plist** et effectuez les modifications suivantes.</span><span class="sxs-lookup"><span data-stu-id="8ec39-144">Open the **Info.plist** file and make the following changes.</span></span>

- <span data-ttu-id="8ec39-145">Dans l’onglet **application** , vérifiez que la valeur de l' **identificateur groupé** correspond à la valeur que vous avez définie pour les **groupes de trousseau** dans les **habilitations. plist**.</span><span class="sxs-lookup"><span data-stu-id="8ec39-145">On the **Application** tab, check that the value of **Bundle identifier** matches the value you set for **Keychain Groups** in **Entitlements.plist**.</span></span> <span data-ttu-id="8ec39-146">Si ce n’est pas le cas, mettez-le à jour maintenant.</span><span class="sxs-lookup"><span data-stu-id="8ec39-146">If it doesn't, update it now.</span></span>
- <span data-ttu-id="8ec39-147">Sous l’onglet **avancé** , recherchez la section **types d’URL** .</span><span class="sxs-lookup"><span data-stu-id="8ec39-147">On the **Advanced** tab, locate the **URL Types** section.</span></span> <span data-ttu-id="8ec39-148">Ajoutez un type d’URL ici avec les valeurs suivantes :</span><span class="sxs-lookup"><span data-stu-id="8ec39-148">Add a URL type here with the following values:</span></span>
  - <span data-ttu-id="8ec39-149">**Identificateur**: défini sur la valeur de votre **identificateur de package**</span><span class="sxs-lookup"><span data-stu-id="8ec39-149">**Identifier**: set to the value of your **Bundle identifier**</span></span>
  - <span data-ttu-id="8ec39-150">**Modèles d’URL**: définis `msal{YOUR-APP-ID}`sur.</span><span class="sxs-lookup"><span data-stu-id="8ec39-150">**URL Schemes**: set to `msal{YOUR-APP-ID}`.</span></span> <span data-ttu-id="8ec39-151">Par exemple, si votre ID d’application `67ad5eba-0cfc-414d-8f9f-0a6d973a907c`est, définissez-le sur `msal67ad5eba-0cfc-414d-8f9f-0a6d973a907c`.</span><span class="sxs-lookup"><span data-stu-id="8ec39-151">For example, if your app ID is `67ad5eba-0cfc-414d-8f9f-0a6d973a907c`, you would set this to `msal67ad5eba-0cfc-414d-8f9f-0a6d973a907c`.</span></span>
  - <span data-ttu-id="8ec39-152">**Rôle**:`Editor`</span><span class="sxs-lookup"><span data-stu-id="8ec39-152">**Role**: `Editor`</span></span>
  - <span data-ttu-id="8ec39-153">**Icon**: laisser vide</span><span class="sxs-lookup"><span data-stu-id="8ec39-153">**Icon**: Leave empty</span></span>

![Capture d’écran de la section types d’URL de info. plist](./images/add-url-type.png)

<span data-ttu-id="8ec39-155">Enfin, mettez à jour le code dans le projet **GraphTutorial. iOS** pour gérer la redirection lors de l’authentification.</span><span class="sxs-lookup"><span data-stu-id="8ec39-155">Finally, update the code in the **GraphTutorial.iOS** project to handle the redirect during authentication.</span></span> <span data-ttu-id="8ec39-156">Ouvrez le fichier **AppDelegate.cs** et ajoutez l’instruction `using` suivante en haut du fichier.</span><span class="sxs-lookup"><span data-stu-id="8ec39-156">Open the **AppDelegate.cs** file and add the following `using` statement at the top of the file.</span></span>

```cs
using Microsoft.Identity.Client;
```

<span data-ttu-id="8ec39-157">Ajoutez la ligne suivante pour `FinishedLaunching` fonctionner juste avant la `LoadApplication(new App());` ligne.</span><span class="sxs-lookup"><span data-stu-id="8ec39-157">Add the following line to `FinishedLaunching` function just before the `LoadApplication(new App());` line.</span></span>

```cs
// Specify the Keychain access group
App.iOSKeychainSecurityGroup = NSBundle.MainBundle.BundleIdentifier;
```

<span data-ttu-id="8ec39-158">Enfin, remplacez la `OpenUrl` fonction pour transmettre l’URL à la bibliothèque MSAL.</span><span class="sxs-lookup"><span data-stu-id="8ec39-158">Finally, override the `OpenUrl` function to pass the URL to the MSAL library.</span></span> <span data-ttu-id="8ec39-159">Ajoutez les éléments suivants à `AppDelegate` la classe.</span><span class="sxs-lookup"><span data-stu-id="8ec39-159">Add the following to the `AppDelegate` class.</span></span>

```cs
// Handling redirect URL
// See: https://github.com/azuread/microsoft-authentication-library-for-dotnet/wiki/Xamarin-iOS-specifics
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

## <a name="storing-the-tokens"></a><span data-ttu-id="8ec39-160">Stockage des jetons</span><span class="sxs-lookup"><span data-stu-id="8ec39-160">Storing the tokens</span></span>

<span data-ttu-id="8ec39-161">Lorsque la bibliothèque d’authentification Microsoft est utilisée dans un projet Xamarin, elle tire parti du stockage sécurisé natif pour mettre en cache les jetons par défaut.</span><span class="sxs-lookup"><span data-stu-id="8ec39-161">When the Microsoft Authentication Library is used in a Xamarin project, it takes advantage of the native secure storage to cache tokens by default.</span></span> <span data-ttu-id="8ec39-162">Pour plus d’informations, consultez [la rubrique sérialisation du cache de jetons](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization) .</span><span class="sxs-lookup"><span data-stu-id="8ec39-162">See [Token cache serialization](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization) for more information.</span></span>

## <a name="test-sign-in"></a><span data-ttu-id="8ec39-163">Tester la connexion</span><span class="sxs-lookup"><span data-stu-id="8ec39-163">Test sign-in</span></span>

<span data-ttu-id="8ec39-164">À ce stade, si vous exécutez l’application et que vous appuyez sur le bouton **de connexion** , vous êtes invité à vous connecter.</span><span class="sxs-lookup"><span data-stu-id="8ec39-164">At this point if you run the application and tap the **Sign in** button, you are prompted to sign in.</span></span> <span data-ttu-id="8ec39-165">Lors de la connexion, le jeton d’accès doit s’afficher dans la sortie du débogueur.</span><span class="sxs-lookup"><span data-stu-id="8ec39-165">On successful sign in, you should see the access token printed into the debugger's output.</span></span>

![Capture d’écran de la fenêtre sortie dans Visual Studio](./images/debugger-access-token.png)

## <a name="get-user-details"></a><span data-ttu-id="8ec39-167">Obtenir les détails de l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="8ec39-167">Get user details</span></span>

<span data-ttu-id="8ec39-168">Ajoutez une nouvelle fonction à la classe **app** pour initialiser le `GraphServiceClient`.</span><span class="sxs-lookup"><span data-stu-id="8ec39-168">Add a new function to the **App** class to initialize the `GraphServiceClient`.</span></span>

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

<span data-ttu-id="8ec39-169">À présent, `SignIn` mettez à jour la fonction dans **app.Xaml.cs** pour appeler `GetUserInfo`cette fonction au lieu de.</span><span class="sxs-lookup"><span data-stu-id="8ec39-169">Now update the `SignIn` function in **App.xaml.cs** to call this function instead of `GetUserInfo`.</span></span> <span data-ttu-id="8ec39-170">Supprimez les éléments suivants `SignIn` de la fonction.</span><span class="sxs-lookup"><span data-stu-id="8ec39-170">Remove the following from the `SignIn` function.</span></span>

```cs
await GetUserInfo();

IsSignedIn = true;
```

<span data-ttu-id="8ec39-171">Ajoutez le code suivant à la fin de `SignIn` la fonction.</span><span class="sxs-lookup"><span data-stu-id="8ec39-171">Add the following to the end of the `SignIn` function.</span></span>

```cs
await InitializeGraphClientAsync();
```

<span data-ttu-id="8ec39-172">À présent, `GetUserInfo` mettez à jour la fonction pour obtenir les détails de l’utilisateur à partir de Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="8ec39-172">Now update the `GetUserInfo` function to get the user's details from the Microsoft Graph.</span></span> <span data-ttu-id="8ec39-173">Remplacez la fonction `GetUserInfo` existante par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="8ec39-173">Replace the existing `GetUserInfo` function with the following.</span></span>

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

<span data-ttu-id="8ec39-174">Si vous enregistrez vos modifications et exécutez l’application maintenant, une fois connecté, l’interface utilisateur est mise à jour avec le nom d’affichage et l’adresse de messagerie de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="8ec39-174">If you save your changes and run the app now, after sign-in the UI is updated with the user's display name and email address.</span></span>
