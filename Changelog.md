Version numbers are synced between hello_imgui and imgui_bundle.

# v1.5.0
* Breaking change: add FontAwesome options with support for v4 and v6:
  - See  e04af395c5a25e20861d06afd9ec0cddcccd7542
  - the default icon font is now FontAwesome 6, which includes many more icons.
  - added runnerParams.runnerCallbacks.defaultIconFont
  - you need to include manually the icons:
     #include "hello_imgui/icons_font_awesome_6.h" or #include "hello_imgui/icons_font_awesome_4.h" 

# v1.4.2

* Integration with vcpkg ongoing (see [PR](https://github.com/microsoft/vcpkg/pull/36501))
* Reviewed CMake backend selection process
You can now build with several rendering backends and several platform backends at the same time.
* Work on vcpk packaging

# v1.4.0

## Vcpkg support for dependencies
You can install almost all required dependencies with [vcpkg](https://github.com/microsoft/vcpkg). 
```bash
# Clone hello_imgui 
git clone https://github.com/pthom/hello_imgui.git
cd hello_imgui
# Clone vcpkg -& bootstrap
git clone https://github.com/microsoft/vcpkg.git
./vcpkg/bootstrap-vcpkg.sh
# Install dependencies required by hello_imgui
./vcpkg/vcpkg install "glad[gl-api-43] stb freetype lunasvg glfw3 sdl2 imgui[opengl3-binding, docking-experimental, glfw-binding, sdl2-binding,freetype, freetype-lunasvg]"
# Build hello_imgui
mkdir build && cd build
cmake .. -DCMAKE_TOOLCHAIN_FILE=../vcpkg/scripts/buildsystems/vcpkg.cmake
cmake --build . -j 4 
```

Notes: 
- this will not support ImGui Test Engine, as it is not available in vcpkg yet.
- See CI Tests: [![VcpkgDeps](https://github.com/pthom/hello_imgui/workflows/VcpkgDeps/badge.svg)](https://github.com/pthom/hello_imgui/actions/workflows/VcpkgDeps.yml)


## Vcpkg packaging
hello_imgui is now ready to be integrated to [vcpkg](https://github.com/microsoft/vcpkg).

You can test this with the following commands:
```bash
# Clone hello_imgui  (just to get the overlay in hello_imgui_cmake/overlay_vcpkg/hello-imgui)
git clone https://github.com/pthom/hello_imgui.git
cd hello_imgui
# Clone vcpkg -& bootstrap
git clone https://github.com/microsoft/vcpkg.git
./vcpkg/bootstrap-vcpkg.sh
# Install hello_imgui via vcpkg (using the overlay)
./vcpkg/vcpkg install "hello-imgui[opengl3-binding, glfw-binding, sdl2-binding]" --overlay-ports=hello_imgui_cmake/overlay_vcpkg/hello-imgui
```

Notes:
- See CI Tests: [![VcpkgPackage](https://github.com/pthom/hello_imgui/workflows/VcpkgPackage/badge.svg)](https://github.com/pthom/hello_imgui/actions/workflows/VcpkgPackage.yml)

## Other
* Update update imgui to  v1.90.1-docking
* Add demo + doc / FontAwesome 6


# v1.3.0

## New Features

* Added EdgeToolbars: see [definition](https://github.com/pthom/hello_imgui/blob/3a279ce7459b04a4c2e7460b844cbf354833964e/src/hello_imgui/runner_callbacks.h#L72-L102), [callbacks](https://github.com/pthom/hello_imgui/blob/3a279ce7459b04a4c2e7460b844cbf354833964e/src/hello_imgui/runner_callbacks.h#L140-L147), [example usage](https://github.com/pthom/hello_imgui/blob/3a279ce7459b04a4c2e7460b844cbf354833964e/src/hello_imgui_demos/hello_imgui_demodocking/hello_imgui_demodocking.main.cpp#L694-L714), and [demo](https://traineq.org/ImGuiBundle/emscripten/bin/demo_docking.html)
* Callbacks: add [EnqueuePostInit, EnqueueBeforeExit, PostInit_AddPlatformBackendCallbacks](https://pthom.github.io/hello_imgui/book/doc_params.html#runnercallbacks)
* Add [renderer_backend_options](https://pthom.github.io/hello_imgui/book/doc_params.html#renderer-backend-options)
* Add support for Extended Dynamic Range (EDR) on macOS : see [PR](https://github.com/pthom/hello_imgui/pull/89). Added [demo / EDR](https://github.com/pthom/hello_imgui/tree/master/src/hello_imgui_demos/hello_edr) - Only works with Metal
* Test Engine: can re-call params.callbacks.RegisterTests
* rememberEnableIdling default=false (true is too surprising)
* emscripten: Use webgl2 / GLES3

# Other, between v1.0.0 and v1.3.0

* Added nice [documentation pages](https://pthom.github.io/hello_imgui)
* Uses [Freetype for font rendering](https://github.com/pthom/hello_imgui/blob/549c205dd3ca98f18fcf541a2ebbfc5abdd10410/CMakeLists.txt#L96-L106)
* Improved [Font Loading utility](https://github.com/pthom/hello_imgui/blob/549c205dd3ca98f18fcf541a2ebbfc5abdd10410/src/hello_imgui/hello_imgui_font.h#L13-L62)
* Added support for Colored font and Emoji fonts ([Demo](https://traineq.org/ImGuiBundle/emscripten/bin/demo_docking.html))
* Can [fully customize the menu bar](https://pthom.github.io/hello_imgui/book/doc_api.html#customize-hello-imgui-menus)
* Added support for macOS application bundles
* Added option to specify where settings are saved: `RunnerParams.iniFolderType` can be set to: `CurrentFolder`, `AppUserConfigFolder`, `DocumentsFolder`, `HomeFolder`, `TempFolder`, `AppExecutableFolder`.
* Support for Application Icon: the file `assets/app_settings/icon.png` will be used to generate the window icon (C++, Python), and app icon (C++ only) for any platform. See assets structure below:
```
assets/
├── world.png                         # A custom asset
├── app_settings/                     # Application settings
│         ├── icon.png                # This will be the app icon, it should be square
│         │                           # and at least 512x512. It will  be converted
│         │                           # to the right format, for each platform.
│         ├── apple/
│         │         └── Info.plist    # macOS and iOS app settings
│         │                          # (or Info.ios.plist + Info.macos.plist)
├── fonts/
│         ├── DroidSans.ttf               # Default fonts
│         └── fontawesome-webfont.ttf     #     used by HelloImGui
│         ├── Roboto
│         │    ├── Roboto-Bold.ttf        # Font used by Markdown
│         │    ├── Roboto-BoldItalic.ttf
│         │    ├── Roboto-Regular.ttf
│         │    └── Roboto-RegularItalic.ttf
│         ├── SourceCodePro-Regular.ttf
├── images
│         └── markdown_broken_image.png
```
* hello_imgui_add_app and imgui_bundle.add_app can now accept ASSETS_LOCATION as a parameter e.g. `hello_imgui_add_app(my_app file1.cpp file2.cpp ASSETS_LOCATION my_assets)`


# v1.0.0

* Integrated ImGui Test Engine
* Layout & docking: can switch between several layout and save their settings separately
* Reduce FPS when not in use to save CPU usage: added FpsIdling options
* Can store custom user preferences
* Greatly improved HighDpi support
* Improved Theming and Themes
* Added callbacks: PostInit / BeforeExit_PostCleanup / PreNewFrame
* MingW compatibility (and CI)
* Make it possible to run without assets/fonts
* Improved emscripten multithread support
