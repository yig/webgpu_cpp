# `webgpu_cpp.h`

This repository provides a platform-independent way to use the nice C++ wrapper created by the [Dawn](https://dawn.googlesource.com/dawn/) WebGPU implementation. The goal is to work with any WebGPU implementation that implements the standard [`webgpu.h`](https://github.com/webgpu-native/webgpu-headers/blob/main/webgpu.h). It works by downloading and running the Dawn generator scripts and data.

**N.B.**: It doesn't currently work, since Dawn-isms are embedded inside the JSON file used to generate the C++ headers. I have started the work of patching them out, but it is unfinished.

## Use

Copy the `include` directory somewhere in your project.

If you are using CMake, you can:

```
FetchContent_Declare(
  webgpu_cpp
  GIT_REPOSITORY https://github.com/yig/webgpu_cpp/
  GIT_TAG        main
  GIT_SHALLOW TRUE
  GIT_PROGRESS TRUE
)
FetchContent_Populate(webgpu_cpp)
FetchContent_GetProperties(webgpu_cpp)
add_library(webgpu_cpp INTERFACE)
target_include_directories(webgpu_cpp INTERFACE ${webgpu_cpp_SOURCE_DIR}/include)
```

Then simply add `webgpu_cpp` to your target's `target_link_library( your_target ... webgpu_cpp )`.

## Known Issues

* Doesn't currently work. I need to remove all the Dawn-isms from `dawn.json`.
* I don't attempt to extract `webgpu_glfw.h`, which declares `wgpu::Surface wgpu::glfw::CreateSurfaceForWindow( const wgpu::Instance& instance, GLFWwindow* window);`. It's definition is too intertwined with the rest of Dawn.

## Regenerate the `include/` directory

The following instructions allow you to regenerate the `include/` directory to track updates to `webgpu.h`.

### Update Dawn scripts and data

Fast:

```
mkdir -p generator
curl 'https://dawn.googlesource.com/dawn/+archive/refs/heads/chromium/6668/generator.tar.gz' | tar -C generator -xzf -
curl 'https://dawn.googlesource.com/dawn/+/refs/heads/chromium/6668/src/dawn/dawn.json?format=TEXT' | base64 -d -o dawn.json
patch dawn.json dawn.json.patch

mkdir -p include/webgpu
curl 'https://dawn.googlesource.com/dawn/+/refs/heads/chromium/6668/include/webgpu/webgpu_enum_class_bitmasks.h?format=TEXT' | base64 -d -o include/webgpu/webgpu_enum_class_bitmasks.h
```

Slow:

```
mkdir -p include/webgpu

git clone https://dawn.googlesource.com/dawn
cd dawn
git checkout chromium/6668
cp -R generator ..
cp src/dawn/dawn.json ..
cp include/webgpu/webgpu_enum_class_bitmasks.h ../include/webgpu
cd ..
```

### Install Python requirements

```
source venv/bin/activate.fish
pip install -r requirements.txt
```

The only dependency is `jinja2@e2d024354e11cc6b041b0cff032d73f0c7e43a07`.

### Re-generate `include/`

```
python3 generator/dawn_json_generator.py --targets cpp_headers --dawn-json dawn.json --template-dir generator/templates --output-dir output
cp output/include/webgpu/webgpu_cpp_chained_struct.h output/include/dawn/webgpu_cpp.h output/include/dawn/webgpu_cpp_print.h include/webgpu
sed -i "" 's/dawn\/webgpu_cpp\.h/webgpu\/webgpu_cpp\.h/' include/webgpu/webgpu_cpp_print.h
```
