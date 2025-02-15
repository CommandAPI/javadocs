# CommandAPI Javadocs

The CommandAPI JavaDocs are manually generated using Doxygen. Doxygen is a documentation generator for C++, which also has some support for Java and a cleaner, more modern interface with support for inheritance diagrams (amongst other features). Doxygen can be downloaded [here](https://www.doxygen.nl/download.html#srcbin).

## Building the JavaDocs

- If necessary, download and install Doxygen as detailed in their documentation [here](https://www.doxygen.nl/manual/install.html).

- Update the git submodules `CommandAPI` and `brigadier-with-javadocs` to point to the version you want to generate javadocs for. Typically, this will be the latest commit on the respective `master` branches. The submodules should already be on the master branch with no local changes, in which case you can simply run `git pull` for each submodule:

```shell
cd CommandAPI
git pull
cd ..

cd brigadier-with-javadocs
git pull
cd ..
```

- If necessary, update the `PROJECT_NUMBER` tag in the `Doxyfile` to the appropriate CommandAPI version.

- Run Doxygen via the terminal or graphical Doxywizard application. For the CLI, simply run `doxygen` in the root of this repository. For the GUI:
    - Go to `File > Open...` and open the `Doxyfile` in the root of this repository
    - Go to the `Run` tab (next to `Wizard` and `Expert`)
    - Press the `Run doxygen` button:

      <img width="500" alt="image" src="https://github.com/JorelAli/CommandAPI/assets/4613171/23c9c82f-bcb8-444e-a7c1-8e25482b1b36">

## Why Doxygen instead of JavaDocs?

I chose to use Doxygen over JavaDocs because Bukkit's original JavaDocs were also generated using Doxygen and I preferred the UI to the standard JavaDocs UI. Additionally:

- Doxygen has inheritance diagrams:

  <img width="789" alt="image" src="https://user-images.githubusercontent.com/4613171/220895985-b98e5424-400d-45ee-943d-abdfc1bd69d7.png">

- Doxygen shows the JavaDocs for inherited methods without requiring you to navigate to the relevant inherited class/interface to view its method descriptions:

  <img width="789" alt="image" src="https://user-images.githubusercontent.com/4613171/220896243-ff2e5056-cacc-448d-8d48-1c437c6ccd4e.png">

- Doxygen makes it easy to pull in other projects into the documentation:

  <img width="778" alt="image" src="https://user-images.githubusercontent.com/4613171/220896833-9ef66762-833d-4a33-95e5-d67a9f09cae0.png">

## Known issues

Java classes with generics that extend other classes aren't properly generated in Doxygen. For example:

```java
public class MyClass<T extends List<String>> {
  public String someField;
}
```

This is currently a known issue on Dogyxen (see [here](https://github.com/doxygen/doxygen/issues/8495)) and development was done to rectify this (see [here](https://github.com/doxygen/doxygen/pull/8534)), but at the time of writing (Feb 2023) no further progress has been done on this issue.

This issue can be circumvented using Doxygen's [`\cond` preprocessor command](https://www.doxygen.nl/manual/commands.html#cmdcond). By applying a `@cond` comment followed by a label, then a subsequent `@endcond` comment around generics, this issue can be avoided. An example of this can be found in the CommandAPI repository at [this commit](https://github.com/CommandAPI/CommandAPI/commit/e6f77968b97133d3739936abdd90fa58e1530b4e).

For example, if you take this class parameterized with generics `T` and `U`, and the `U` generic extends another class, this will cause Doxygen to skip all methods declared in `MyClass`:

```java
public class MyClass<T, U extends SomeOtherClass<T>> {
  ...
}
```

You can fix this by adding the relevant `\cond` guards. By default, the CommandAPI uses the label `DOX`:

```java
public class MyClass<T, U
/// @cond DOX
extends SomeOtherClass<T>
/// @endcond
> {
  ...
}
```

## Brigadier JavaDocs

As the CommandAPI heavily depends on [Mojang/brigadier](https://github.com/Mojang/brigadier), the CommandAPI's JavaDocs also includes Brigadier's JavaDocs. JavaDocs for Brigadier were kindly written by [I-Al-Istannen](https://github.com/I-Al-Istannen) because the main Brigadier repository doesn't have any of their own JavaDocs.

JorelAli currently hosts a fork of [Mojang/brigadier](https://github.com/Mojang/brigadier) which is up-to-date, and incorporates the changes from [I-Al-Istannen/brigadier](https://github.com/I-Al-Istannen/brigadier) which is compatible with Doxygen (it has the `@cond DOX` guards). The fork can be found at [CommandAPI/brigadier-with-javadocs](https://github.com/CommandAPI/brigadier-with-javadocs), and is included as a submodule of this repository.
