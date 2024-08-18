# Unity BepInEx Modding Tutorial

## Introduction
Hello, and welcome to my all-in-one Unity mod making tutorial. This video will cover everything you need to know to get started with modding Unity games using BepInEx.

## Preamble
Before we get started, I want to make a few things clear. This tutorial is aimed at beginners, so I will be explaining everything in detail. However, I will assume that you have some basic knowledge of C# programming. If you don't, I recommend checking out some C# tutorials before continuing. You can find a link to a good C# tutorial in the description below. Knowledge of Unity is also helpful, but not required.

## Prerequisites
Before we can start modding Unity games, we need to install a few tools. Here's what you'll need:
- Any C# IDE, such as Visual Studio or Rider
- An assembly viewer, such as dnSpy or ILSpy
- An assembly publicizer, link in the description
- And of course, a Unity game to mod
BepInEx is also required, but we'll cover that in the next section.

## What is BepInEx?
BepInEx is a popular modding framework for Unity games. It provides a set of tools and libraries that make it easier to create mods for Unity games. BepInEx is widely used in the modding community and is compatible with many Unity games.

## How Does BepInEx Work?
BepInEx works by injecting code into the game's assembly at runtime. This allows modders to access and modify the game's code without altering the original files. It uses a combination of MonoMod and Harmony to patch the game's code and apply the mods. BepInEx also provides a console log that allows modders to debug their mods. In general, Harmony is recommended to use since it makes patching the game's code easier and more reliable. MonoMod is more powerful but also more complex and should only be used when necessary. We will dive deeper into these concepts later in the tutorial when we start writing mods.

## Installing BepInEx
To install BepInEx, you'll need to download the latest version from the BepInEx GitHub page. You can find a link to the GitHub page in the description below. Please note that BepInEx has two main versions: BepInEx 5 and BepInEx 6. If you are modding a game that already has mods available, check which version of BepInEx they are using and use the same version to avoid compatibility issues. Generally, BepInEx 5 is used for Mono-based games, while BepInEx 6 is used for IL2CPP-based games, but this can vary depending on the game. For this tutorial, we'll be using BepInEx 5 since BepInEx 6 is still in beta at the time of recording and we will be modding a Mono-based game. The process for modding IL2CPP-based games is similar, but there are some differences that will be covered later in the tutorial.
Once you've downloaded the zip file, extract it to your game's root directory - the folder where the game's executable is located. Make sure the `BepInEx` folder and `winhttp.dll` are in the root directory. That's it! BepInEx is now installed and ready to use.

## Creating Your First Mod
Now that we have BepInEx installed, let's create our first mod. We will be using a generic BepInEx template, which I designed myself to help beginners get started and will be linked in the description. Some community templates are also available which might be more suitable for specific games, but for this tutorial, we'll stick with this generic template.
We'll extract the template to a new folder and open it in our C# IDE. We first need to add a reference to the game's assembly. This is the DLL file that contains the game's code. Note that the following steps only apply to Mono-based games. IL2CPP-based games require a different approach, which will be covered later in the tutorial.

## Publicizing the Assembly (Mono-based Games Only)
Before we can add a reference to the game's assembly, we should publicize it. While this step is not strictly necessary, it makes modding easier by exposing more of the game's code to us. A publicizer tool makes private and internal classes and methods public, allowing us to access them in our mods, which can be quite useful and is easier than using reflection. Publicized assemblies also are safer to share with others, as all the method bodies are removed.
Some games already come with publicized assemblies on NuGet or other platforms. If this is the case, you can skip this step and simply add a reference to the existing publicized assembly. If not, you'll need to publicize the assembly yourself.
You can find a link to a publicizer tool in the description below. To publicize the assembly, simply drag and drop the game's DLL file onto the publicizer tool. It will create a new publicized DLL file that we can reference in our mods.

## Adding a Reference to the Game's Assembly (Mono-based Games Only)
Now that we have a publicized version of the game's assembly, we can add a reference to it in our mod project. It's recommended to copy the publicized DLL to a `lib` folder in your mod project to keep things organized. In your C# IDE, right-click on the project and select `Add Reference`. Browse to the publicized DLL file and add it as a reference. Now we can access the game's code from our mod.

## Setting Up the Mod
With the game's assembly referenced, we can now start writing our mod. It's usually a good idea to specify all the properties of the mod first, like the name and version. We'll start by renaming the project file to match our mod's name. In our case, we'll just call it `TutorialMod.csproj`. Next, we'll open this file in a text editor and change the `AssemblyName`, `Product`, and `RootNamespace` to match our mod's name. If you are using a different template, some other properties might need to be changed as well or may be located in a different file. Also check that an `AllowUnsafeBlocks` property is set to `true`. This is required for accessing private and internal members of the game's assembly.
Next, we'll open the `Plugin.cs` file and change the namespace and plugin info to match our mod's name. The namespace can be anything you want, but it's usually a good idea to use a the mod's name or something related to it. After that, we'll change the `BepInPlugin` section to match our mod's metadata.
The plugin info contains the mod's metadata; PluginGuid is a unique identifier for the mod. This can be any string, but it's recommended to use either the format:
`yourname.gamename.modname`
or reverse domain name notation, for example:
`com.mywebsite.modname` or `com.github.username.modname` if you don't have a domain. It absolutely must be unique to your mod, and may not be changed once you've released the mod.
The PluginName is the name of the mod that will be displayed in the BepInEx console. Again, this can be anything you want. Spaces are allowed, though I personally keep it to one word.
The PluginVersion is the version of the mod. It must be in the semantic versioning format: `major.minor.patch`.
Some templates may change where the plugin info is located or how it's defined. The `BepInPlugin` attribute should either contain the strings directly or reference them from a constant field.

## Writing the Mod Code
Now that we have everything set up, we can start writing our mod's code. There are many things you can do with mods, and many ways to do them. This tutorial will cover most of the common modding techniques; Harmony prefixes, postfixes, and transpilers, as well as using MonoMod for more complex patches. We'll also cover how to use the BepInEx console to debug your mods and how to package and distribute your mods. First, let's start with learning about Harmony prefixes and postfixes.

## Harmony Prefixes and Postfixes Introduction
Harmony prefixes and postfixes are the simplest and most common way to patch methods in Unity games. They allow you to run custom code before or after the original method, respectively. This is useful for modifying the behavior of the game without changing the original code. They are easy to write and understand, making them a good starting point for beginners. We'll start by writing a simple prefix patch for the game Inscryption.

## Writing a Harmony Prefix Patch
In this example, we'll write a simple prefix patch for the game Inscryption. We will write a patch that sends a message to the console whenever the player draws a card. This is a simple example to demonstrate how Harmony prefixes work. First, we need to find the method we want to patch. This can be done using the assembly viewer. First, open the game's assembly in the assembly viewer. The assembly can be found in `Game Name_Data/Managed/Assembly-CSharp.dll`. Once you've opened the assembly, you can search for the method you want to patch. In the `Search` tab, type the method name or part of it to find the method. We need to do some guesswork here, but usually, the method name is descriptive enough to find it. Since the name `Draw` is quite common and assembly viewers typically search all assemblies by default, we should scope search the game assembly by right-clicking on the assembly and selecting `Scope search this assembly`. This will limit the search to the game's assembly. There still are a lot of results, but we can see two `Deck.Draw()` methods. Looking into both of them, we can see that the second one just calls the first one with a `null` argument, so we should patch the first one.

## Patching the Method
Now that we've found the method we want to patch, we can write the Harmony patch. Open the `Plugin.cs` file in your mod project and add the following code:
```csharp
[HarmonyPatch(typeof(Deck), nameof(Deck.Draw), new Type[] { typeof(CardInfo) })]
internal static void Deck_Draw_Prefix()
{
    Log.LogInfo("Player drew a card!");
}
```
Note that Harmony patches can be defined in different ways. This is just my preferred way of defining them. It's important that the class containing the patch method has the `HarmonyPatch` attribute and that the patch method is static, even if the original method is not. The `typeof(Deck)` part specifies the class that contains the method we want to patch, and `nameof(Deck.Draw)` specifies the method we want to patch. You can also use the string representation of the method name. The `new Type[] { typeof(CardInfo) }` part specifies the method signature. If there are multiple overloads of the method, which is the case here, you need to specify the signature to ensure the correct method is patched. If there is only one overload, you can leave this part out.

## Cancelling the Original Method
In some cases, you may want to cancel the original method after running your prefix patch. This can be done by returning `false` from the prefix patch. This will prevent the original method from running. We'll modify our patch to cancel the original method:
```csharp
[HarmonyPatch(typeof(Deck), nameof(Deck.Draw), new Type[] { typeof(CardInfo) })]
internal static bool Deck_Draw_Prefix()
{
    Log.LogInfo("No cards for you!");
    return false;
}
```
Now, when the player tries to draw a card, the message "No cards for you!" will be sent to the console, and no card will be drawn. Sucks to be you, player!
I suppose not drawing cards kind of defeats the purpose of the game, so let's remove the patch and move on to Harmony postfixes.

## Writing a Harmony Postfix Patch
Harmony postfixes are similar to prefixes but run after the original method. Unlike prefixes, postfixes can't cancel the original method, but they have a special variant called the pass-through postfix that can modify coroutines. We'll write a simple postfix patch that sends a message to the console after the player draws a card with the card's name and how many cards are left in the deck.

## Special Parameters
Since the card that's drawn is the return value of the method, we need to use a special parameter to access it in the postfix patch. This parameter is called `__result`. Similarly, since the number of cards left in the deck is a field in the `Deck` class, we need to use another special parameter called `__instance` to access it. This parameter is also automatically passed to the postfix patch by Harmony. You can also access the original method's arguments by simply adding them as parameters to the patch method using the same names as in the original method. This isn't necessary for this example though.
We'll write the postfix patch now:
```csharp
[HarmonyPatch(typeof(Deck), nameof(Deck.Draw), new Type[] { typeof(CardInfo) })]
internal static void Deck_Draw_Postfix(ref CardInfo __result, Deck __instance)
{
    Log.LogInfo($"Player drew a {__result.DisplayedNameLocalized}! {__instance.cards.Count} cards left in the deck.");
}
```
The `ref` keyword is used to pass the `__result` parameter by reference, allowing us to modify it if needed. In this case, we're just reading from it and it's an object, so it's already passed by reference, but it's good practice to use `ref` anyway.

## Pass-Through Postfixes
Pass-through postfixes are a special type of postfix that can modify coroutines. Unity uses coroutines for asynchronous tasks, such as animations or waiting for a certain amount of time, and they can be identified by their return type of `IEnumerator`. Pass-through postfixes allow you to modify the behavior of coroutines. For our example, we'll write a pass-through postfix that makes Leshy say "Pick a card, any card!" when the player activates a "Tutor" card, letting them pick a card from the deck. First, we need to find the method we want to patch. Since we already know where the `Deck` class is, we can quickly find the `ChooseCard` method in the `Deck` class. We'll write the pass-through postfix now:
```csharp
[HarmonyPatch(typeof(Deck), nameof(Deck.ChooseCard))]
internal static IEnumerable Deck_ChooseCard_Postfix(IEnumerable __result)
{
    Singleton<TextDisplayer>.Instance.ShowUntilInput("Pick a card, any card!");
    yield return __result;
}
```
The `yield return __result;` line is important. It tells the coroutine to run the original method. This technically makes this patch a prefix since our code runs before the original method, but it's still considered a postfix due to the way coroutines work. You can also write code after the `yield return __result;` line to run after the original method. For example, we could show a message after the player picks a card like "Total misplay!".

## Transpilers
Transpilers are a more advanced way to patch methods in Unity games. We have been working with C# code so far, but actually, I need to let you in on a little secret: the assembly code that Unity games run is not C# code, but IL code. You've just been fooled this whole time! Assembly viewers just convert the IL code to C# for easier reading and writing, and Harmony prefixes and postfixes just get converted to IL code before being injected. If we want to modify an existing method, or add some code in the middle of it, we need to write IL code since we can't directly modify the C# code - it's not actually there!
