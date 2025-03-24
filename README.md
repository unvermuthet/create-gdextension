# create-gdextension

> My spin on godot-cpp-template!

## Customizing

Rename all instances of `my-gdextension` and `my_gdextension` to your preferred name. **Match the case and don't forget the folder and file under `project/`.**

## Building

To help you get going, I've configured a [Development Container](https://containers.dev/) with everything setup to target Windows or Linux. Just run `scons` or try `scons --help`!

If you want to configure the environment yourself, reference the setup from the Dev Container or follow [Godot's "Building from source" Guide](https://docs.godotengine.org/en/latest/contributing/development/compiling/).

Reap the rewards of disabling classes in `build_profile.json`! This can greatly speed up a cold-start compile. You can also change `disabled_classes` to `enabled_classes`, but don't be surprised by compile errors for missing classes!

## GitHub Actions workflow

Every push triggers a CI build. Pushes tagged with a semantic version (`v*.*.*`) also create a draft release.
