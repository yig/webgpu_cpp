## Update Dawn stuff

### Via: <https://developer.chrome.com/docs/web-platform/webgpu/build-app>

Slow:

```
git clone https://dawn.googlesource.com/dawn
cd dawn
git checkout chromium/6668
cp -R generator ..
cp -R src/dawn/dawn.json ..
cd ..
```

Fast:

```
mkdir generator
curl 'https://dawn.googlesource.com/dawn/+archive/refs/heads/chromium/6668/generator.tar.gz' | tar -C generator -xzf -
curl 'https://dawn.googlesource.com/dawn/+/refs/heads/chromium/6668/src/dawn/dawn.json?format=TEXT' | base64 -d -o dawn.json
```

## Install Python

```
source venv/bin/activate.fish
pip install -r requirements.txt
```

The only dependency is `jinja2@e2d024354e11cc6b041b0cff032d73f0c7e43a07`.

## Generate

```
cd generator
python3 dawn_json_generator.py --targets cpp_headers --dawn-json ../dawn.json
```
