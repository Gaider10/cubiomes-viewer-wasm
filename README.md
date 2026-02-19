https://github.com/cubitect/cubiomes-viewer compiled for wasm  
The only modification is the addition of coi-serviceworker.min.js for CORS on github pages

Generated with
```sh
docker run -it --name cubiomes-viewer ubuntu:24.04

apt update
DEBIAN_FRONTEND=noninteractive apt install -y python3 python3-pip python3-venv git libglib2.0-0t64

mkdir project
cd project

python3 -m venv .venv
. .venv/bin/activate
pip install aqtinstall
aqt install-qt linux desktop 6.10.2 linux_gcc_64
aqt install-qt all_os wasm 6.10.2 wasm_multithread

git clone https://github.com/emscripten-core/emsdk.git
cd emsdk
./emsdk install 4.0.7
./emsdk activate 4.0.7
. emsdk_env.sh
cd ..

git clone https://github.com/cubitect/cubiomes-viewer.git --recursive
cd cubiomes-viewer
mkdir build
cd build
../../6.10.2/wasm_multithread/bin/qmake ..
make -j

tar -czf cubiomes-viewer.tar.gz --transform 's,^,cubiomes-viewer/,' cubiomes-viewer.html cubiomes-viewer.js cubiomes-viewer.wasm qtloader.js qtlogo.svg

docker cp cubiomes-viewer:/project/cubiomes-viewer/build/cubiomes-viewer.tar.gz .
tar -xzf cubiomes-viewer.tar.gz
cd cubiomes-viewer
wget https://raw.githubusercontent.com/gzuidhof/coi-serviceworker/refs/heads/master/coi-serviceworker.min.js
sed -i 's,<body onload="init()">,<body onload="init()"><script src="coi-serviceworker.min.js"></script>,' cubiomes-viewer.html
mv cubiomes-viewer.html index.html
```