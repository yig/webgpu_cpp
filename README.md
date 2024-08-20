## Update Dawn stuff

### Via: <https://developer.chrome.com/docs/web-platform/webgpu/build-app>

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

Fast:

```
mkdir -p generator
curl 'https://dawn.googlesource.com/dawn/+archive/refs/heads/chromium/6668/generator.tar.gz' | tar -C generator -xzf -
curl 'https://dawn.googlesource.com/dawn/+/refs/heads/chromium/6668/src/dawn/dawn.json?format=TEXT' | base64 -d -o dawn.json
patch dawn.json dawn.json.patch

mkdir -p include/webgpu
curl 'https://dawn.googlesource.com/dawn/+/refs/heads/chromium/6668/include/webgpu/webgpu_enum_class_bitmasks.h?format=TEXT' | base64 -d -o include/webgpu/webgpu_enum_class_bitmasks.h
```

## Install Python

```
source venv/bin/activate.fish
pip install -r requirements.txt
```

The only dependency is `jinja2@e2d024354e11cc6b041b0cff032d73f0c7e43a07`.

## Generate

```
python3 generator/dawn_json_generator.py --targets cpp_headers --dawn-json dawn.json --template-dir generator/templates --output-dir output
cp output/include/webgpu/webgpu_cpp_chained_struct.h output/include/dawn/webgpu_cpp.h output/include/dawn/webgpu_cpp_print.h include/webgpu
sed -i "" 's/dawn\/webgpu_cpp\.h/webgpu\/webgpu_cpp\.h/' include/webgpu/webgpu_cpp_print.h
```
