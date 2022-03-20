Set up Ghidra 10 for Vita decompilation
=======================================

TODO 2022-03-20 fix formatting

VitaLoader
^^^^^^^^^^

1. On main Ghidra window (not a project CodeBrowser window), `Edit -> Plugin
   Path...`, `Add Jar ...`, YamlBeans JAR (adding dir apparently doesn't work)
2. On CodeBrowser window, `Window -> Script Manager`, `Manage Script
   Directories` button at top right, add directory holding `VitaLoader.java`
3. Follow this lol w/e https://github.com/xerpi/GhidraVitaLoader
