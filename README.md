# Just a simple linux inside your browser using v86

Nothing to explain actually, I just took the needed file from an already made v86 web-based linux. Striping many stuffs!

## How to run

You might wanna run a local server to avoid `XMLHttpRequest` being blocked for the web assemby `.wasm`

+ For python server

```bash

python3 -m http.server [port]

```
+ For node.js server

```bash

npx http-server -p [port]

```

## Sources

+ For vmlinuz:

```bash

curl -O http://tinycorelinux.net/15.x/x86/release/distribution_files/vmlinuz 

```

+ initramfs.cpio.gz it will need busybox

```bash

mkdir -p custom_rootfs/{bin,sbin,dev,proc,sys,etc,usr/bin,usr/sbin}

cd custom_rootfs

cat << 'EOF' > init
#!/bin/sh
mount -t proc     none /proc
mount -t sysfs    none /sys
mount -t devtmpfs none /dev 2>/dev/null || true

for tty in /dev/ttyS0 /dev/console /dev/tty1 /dev/tty; do
    if [ -c "$tty" ]; then
        exec <"$tty" >"$tty" 2>&1
        break
    fi
done

echo ""
echo "hlinux v86 Booted Successfully"
exec /bin/sh -i
EOF

chmod +x init

wget -O bin/busybox https://busybox.net/downloads/binaries/1.35.0-i686-linux-musl/busybox

chmod +x bin/busybox

cd bin

./busybox --install .

cd ..

find . -print0 | cpio --null -ov --format=newc | gzip -9 > ../v86_assets/initramfs.cpio.gz
cd ..
```

+ For other files:

    Refer `https://copy.sh/v86/build` and their GtHub `https://github.com/copy/v86`.

## Credits

To [Copy](https://github.com/copy/v86)

## LICENCE

Distributed under the MIT Licence.
