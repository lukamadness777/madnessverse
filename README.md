# Madnessverse

[![Release](https://jitpack.io/v/lukamadness777/madnessverse.svg)](https://jitpack.io/#lukamadness777/madnessverse)

Madnessverse is an API mod for Minecraft (Fabric and NeoForge, via Architectury) that allows
other mods to add and remove dimensions during server runtime. It's a Fabric/NeoForge port of
commoble's [Infiniverse](https://github.com/commoble/infiniverse), kept source-compatible with
it as far as reasonably possible.

Madnessverse is primarily a server mod and not required on clients (it is also compatible with
vanilla clients). However, if the Madnessverse mod is present on your client, any new/removed
dimensions will be immediately available in command suggestions for commands that take
dimensions as arguments (such as `/execute in`). If Madnessverse is not present on your client,
you will need to log out of the server and log in for these changes to be visible in command
suggestions.

Built mod jars are available here:

* [CurseForge](https://www.curseforge.com/minecraft/mc-mods/madnessverse)
* [Modrinth](https://modrinth.com/mod/madnessverse)

## Dependency Setup

To use Madnessverse in your mod's development environment, Madnessverse is published via
[JitPack](https://jitpack.io). Because Madnessverse is built with Architectury, published
artifacts are split by platform -- depend on `common` plus whichever platform module matches
your own:

```gradle
repositories {
	maven { url "https://jitpack.io" }
}

dependencies {
	// common (loader-agnostic) module -- this is where MadnessverseAPI lives
	modImplementation "com.github.lukamadness777.madnessverse:madnessverse-common:${madnessverse_version}"

	// and ONE of the following, matching your own platform module:
	modImplementation "com.github.lukamadness777.madnessverse:madnessverse-fabric:${madnessverse_version}"
	// or
	modImplementation "com.github.lukamadness777.madnessverse:madnessverse-neoforge:${madnessverse_version}"
}
```

`madnessverse_version` is any published [GitHub release tag](https://github.com/lukamadness777/madnessverse/releases)
(e.g. `v1.0.0`), a commit hash, or `master-SNAPSHOT` for the latest commit on `master`. See
[jitpack.io/#lukamadness777/madnessverse](https://jitpack.io/#lukamadness777/madnessverse) for
the full list of published versions and modules.

If you only need Madnessverse in your dev environment during runtime and don't want to compile
against the API, you can alternatively use [CurseMaven](https://www.cursemaven.com/) to depend on
a specific built mod jar from CurseForge instead.

## Using the API

### MadnessverseAPI

Dependant mods can use `dev.lukamadness.madnessverse.api.MadnessverseAPI.get()` to get the
Madnessverse API, which allows dimensions to be added or removed during server runtime.

This should generally only be used for dimensions that need some sort of user input to determine
how they should be created; static dimensions can be created using standard dimension jsons
instead of using the Madnessverse API. See
[Custom dimension](https://minecraft.wiki/w/Custom_dimension) on the Minecraft Wiki.

```java
ServerLevel level = MadnessverseAPI.get().getOrCreateLevel(server, levelKey, () -> myLevelStem);

// ...later...
MadnessverseAPI.get().markDimensionForUnregistration(server, levelKey);
```

### UnregisterDimensionEvent

Fired via Architectury's event system when a dimension/level is about to be unregistered and
removed from the server. This fires identically regardless of whether Madnessverse is running on
Fabric or NeoForge. Returning an interrupting `EventResult` (e.g. `EventResult.interruptFalse()`)
from a listener cancels the unregistration of that specific level for that tick.

```java
UnregisterDimensionEvent.EVENT.register(level -> {
	// ...
	return EventResult.pass();
});
```

## License

Madnessverse is licensed under the MIT License. See [LICENSE](LICENSE) for details.
