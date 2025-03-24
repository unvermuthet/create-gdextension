#!/usr/bin/env python
import os
import sys

from methods import print_error

libname = "my-gdextension"
bindir = f"project/addons/{libname}/bin" # Github Action workflow expects this path

localEnv = Environment(tools=["default"], PLATFORM="")

# Build profiles can be used to decrease compile times.
# The file starts out empty, add to it as needed.
# You can either specify "disabled_classes", OR
# explicitly specify "enabled_classes" which disables all other classes.
localEnv["build_profile"] = str("build_profile.json")

customs = ["custom.py"]
customs = [os.path.abspath(path) for path in customs]

opts = Variables(customs, ARGUMENTS)
opts.Update(localEnv)

Help(opts.GenerateHelpText(localEnv))

# Check if the godot-cpp submodule is initialized

submodule_initialized = False
if os.path.isdir("godot-cpp"):
    if os.listdir("godot-cpp"):
        submodule_initialized = True

if not submodule_initialized:
    print_error("""godot-cpp is not available within this folder, as Git submodules haven't been initialized.
Run the following command to download godot-cpp:

    git submodule update --init --recursive""")
    sys.exit(1)

# Extend the godot-cpp environment
env = localEnv.Clone()
env = SConscript("godot-cpp/SConstruct", {"env": env, "customs": customs})

# Sources
env.Append(CPPPATH=["src/"])
sources = Glob("src/*.cpp")

# Add docs
if env["target"] in ["editor", "template_debug"]:
    try:
        doc_data = env.GodotCPPDocData(
            "src/gen/doc_data.gen.cpp", source=Glob("doc_classes/*.xml"))
        sources.append(doc_data)
    except AttributeError:
        print("Not including class reference as we're targeting a pre-4.3 baseline.")

# Set paths
file = "{}{}{}".format(libname, env["suffix"], env["SHLIBSUFFIX"])
libraryfile = "{}/{}/{}".format(bindir, env["platform"], file)

Default(env.SharedLibrary(libraryfile, source=sources))
