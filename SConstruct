#!/usr/bin/env python
import os
import sys
from SCons.Script import Environment, SConscript, Default, Glob, Variables, PathVariable, Import, Copy

extensionName = "my-gdextension"

try:
    Import("env")
except Exception:
    env = Environment()

# Build profiles can be used to decrease compile times.
# You can either specify "disabled_classes", OR
# explicitly specify "enabled_classes" which disables all other classes.
# Modify the example file as needed and uncomment the line below or
# manually specify the build_profile parameter when running SCons.

# env["build_profile"] = env.File("build_profile.json").path

opts = Variables()

opts.Add(PathVariable(
    key="install_dir",
    help="Directory to copy the addon folder into",
    default=env.get("install_dir", None),
    validator=PathVariable.PathIsDirCreate
))

opts.Update(env)
env.Help(opts.GenerateHelpText(env))

# Check if the godot-cpp submodule is initialized
if not (os.path.isdir("godot-cpp") and os.listdir("godot-cpp")):
    print("""godot-cpp is not available within this folder, as Git submodules haven't been initialized.
Run the following command to download godot-cpp:

    git submodule update --init --recursive""")
    sys.exit(1)

# Consume the godot-cpp environment
env = SConscript("godot-cpp/SConstruct", { "env": env })

# Sources
env.Append(CPPPATH=["src/"])
sources = Glob("src/*.cpp")

# Add docs
if env["target"] in ["editor", "template_debug"]:
    try:
        docSources = env.GodotCPPDocData("src/gen/doc_data.gen.cpp", source=Glob("doc_classes/*.xml"))
        sources.append(docSources)
    except AttributeError:
        print("Not including class reference as we're targeting a pre-4.3 baseline.")

# Output shared library
addonFolder = f"project/addons/{extensionName}"
libraryFile = f"{extensionName}{env['suffix']}{env['SHLIBSUFFIX']}"
libraryPath = f"{addonFolder}/bin/{env['platform']}/{libraryFile}"
library = env.SharedLibrary(libraryPath, source=sources)

# Copy the addonFolder to install_in
if env.get("install_dir", None):
    install = env.Install(
        env["install_dir"],
        addonFolder,
    )
    Default([library, install])
else:
    Default([library])
