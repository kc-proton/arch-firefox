su - vscode

---

mkdir ~/build-firefox && cd ~/build-firefox

---

https://gitlab.archlinux.org/archlinux/packaging/packages/firefox/-/archive/153.0.3-1/firefox-153.0.3-1.tar

---

gpg --keyserver keyserver.ubuntu.com --recv-keys 5ECB6497C1A20256

---

makepkg -s 2>&1 | tee build_output.log

---




## 2. Turn Off Heavy Debug Symbol Generation
Firefox's build configurations default to generating full uncompressed binary debug configurations. This creates gigabytes of tracking variables that flood RAM linkers during compilation.
Alter your /etc/makepkg.conf file to strip debugging data out of memory processing matrices:
```
# Strip debuginfo from the build flags entirely

sudo sed -i 's/debug/!debug/g' /etc/makepkg.conf
sudo sed -i 's/strip/!strip/g' /etc/makepkg.conf
```
## 3. Throttle Your Build System Variables
Force the compiler to use a slower, less parallel build architecture to lower peak RAM usage spikes. Create or edit a file named .mozconfig in your Firefox root source directory (/home/vscode/build-firefox/firefox-153.0.3-1/src/firefox-153.0.3/.mozconfig) and add these strict resource caps:
```
# Add these lines into your .mozconfig configuration file

ac_add_options --disable-debug
ac_add_options --disable-tests
ac_add_options --enable-release

# Throttle the Rust compiler to handle code structures sequentially 
export RUSTFLAGS="-C codegen-units=2 -C debuginfo=0 -C opt-level=2"
```


