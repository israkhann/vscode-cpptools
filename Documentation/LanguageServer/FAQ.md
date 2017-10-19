# Frequently asked questions
Tried everything but didnt working the error is #include error detected Intellince feature for this translation unit (C:\mingw/hello) will be provided by the Tag parser.

[How do I get IntelliSense to work correctly](#how-do-i-get-intellisense-to-work-correctly)?

[How do I get the new IntelliSense to work with MinGW on Windows](#how-do-i-get-the-new-intellisense-to-work-with-mingw-on-windows)?

[What is the difference between "includePath" and "browse.path" in c\_cpp\_properties.json](#what-is-the-difference-between-includepath-and-browsepath-in-c_cpp_propertiesjson)?

[How do I re-create the IntelliSense database](#how-do-i-re-create-the-intellisense-database)?

## How do I get IntelliSense to work correctly?

There are two IntelliSense engines present in the extension: the "fuzzy" engine (or Tag Parser), and the new "Default" engine. If you are using version 0.11.0 or higher of the cpptools extension, then you can preview our new IntelliSense engine which has more accurate auto-complete suggestions and tooltips. To use the new engine, you need to ensure that `"C_Cpp.intelliSenseEngine"` is set to `"Default"` in your settings. Since the engine is still in preview it is not on by default for everyone yet.

After selecting the IntelliSense engine that you prefer, take a look at the Problems window in VS Code to see if you need to do any further configuration for your folder. For example, the Default engine will not provide squiggles and auto-complete suggestions for a translation unit (read: a source file and its dependencies) if the include path is not configured properly. You can select any of the problems in the window to navigate to the line when the problem was detected and a lightbulb will appear in the editor with some options (code actions) to help you resolve the problem.

1. Update your includePath (and preprocessor defines)
2. Force semantic IntelliSense

#### Update your includePath (and preprocessor defines)

Selecting this option will open a file called c_cpp_properties.json. If you haven't created this file already, it will be created for you in the .vscode folder of your workspace.

Add the necessary paths to your include files to the `"includePath"` array. The `${workspaceRoot}` variable is available to use to get a relative path to the folder you have opened. Also add any required symbols that need to be defined to the `"defines"` array. Both "\<var\>" and "\<var\>=\<value\>" syntax is accepted. When you edit and save this file, the IntelliSense engine will reset and reparse source your source files and headers with the new settings.

#### Force semantic IntelliSense

If you want IntelliSense to operate on your files even when all #include directives do not resolve, then you can choose the `Force semantic IntelliSense` code action to always use the new IntelliSense engine. You can also set the `C_Cpp.intelliSenseEngineFallback` setting to `"Disabled"`.

## How do I get the new IntelliSense to work with MinGW on Windows?

Since MinGW is a relative of GCC, Microsoft mode compilation (which is the default on Windows) doesn't work very well with it. To use GCC/CLang mode, set the `"intelliSenseMode"` property in your **c_cpp_properties.json** file to `"clang-x64"`. An example **c_cpp_properties.json** [is shared here for your convenience](https://github.com/Microsoft/vscode-cpptools/blob/master/Documentation/LanguageServer/MinGW.md).

## What is the difference between "includePath" and "browse.path" in c\_cpp\_properties.json?

Starting with version 0.11.0 of the cpptools extension, there are now two settings in the c\_cpp\_properties.json file. They are used by the different IntelliSense engines that we support and have slightly different meanings for the components that use them.

The active IntelliSense engine is controlled via the `"C_Cpp.intelliSenseEngine"` setting in your settings.json file. The valid values for this setting are:
* `"Default"`
* `"Tag Parser"`

**includePath**: This array of path strings is used by the new "Default" IntelliSense engine that was introduced in version 0.11.0 of the extension. This new engine provides semantic-aware IntelliSense features and will be the eventual replacement for the Tag Parser that has been powering the extension since it was first released. It currently provides tooltips and error squiggles in the editor. The remaining features (e.g. code completion, signature help, go to definition, ...) are implemented using the Tag Parser's database, so it is still important to ensure that the browse.path setting is properly set.

The paths that you specify for this setting are the same paths that you would send to your compiler via the `-I` switch. When your source files are parsed, the IntelliSense engine will prepend these paths to the files specified by your `#include` directives while attempting to resolve them. These paths are _not searched recursively_.

**browse.path**: This array of path strings is used by the "Tag Parser" (a.k.a. "browse engine"). This engine will _recursively_ enumerate all files under the paths specified and track them as potential includes while tag parsing your project folder. To disable recursive enumeration of a path, you can append a `/*` to the path string.

When you open a workspace for the first time, the extension adds `${workspaceRoot}` to both arrays. If this is undesirable, you can open your c_cpp_properties.json file and remove it.

## How do I re-create the IntelliSense database?

Starting in version 0.12.3 of the extension, we added a command that will reset your IntelliSense database. Open the command palette and choose the "C/Cpp: Reset IntelliSense Database" command.
