
<p align="center">
  <img src="https://github.com/flatpak/flatpak/blob/master/flatpak.png?raw=true" alt="Flatpak icon"/>
</p>

Flatpak is a system for building, distributing and running sandboxed
desktop applications on Linux.

See http://flatpak.org/ for more information.

Read documentation for the flatpak [commandline tools](http://flatpak.github.io/flatpak/flatpak-docs.html) and for the libflatpak [library API](http://flatpak.github.io/flatpak/reference/html/index.html).

# INSTALLATION

Flatpak uses a traditional autoconf-style build mechanism. To build just do
```
 ./configure [args]
 make
 make install
```

Most configure arguments are documented in ./configure --help. However, there
are some options that are a bit more complicated.

Flatpak relies on a project called
[bubblewrap](https://github.com/projectatomic/bubblewrap) for the
lowlevel sandboxing.  By default, an in-tree copy of this is built
(distributed in the tarball or using git submodules in the git
tree). This will build a helper called flatpak-bwrap. If your system
have a recent enough version of bubblewrap already, you can use
`--with-system-bubblewrap` to use that instead.

Bubblewrap can run in two modes, either using unprivileged user
namespaces. This requires that the kernel supports this, which some
distributions disable. For instance, Arch completely disables user
namespaces, while Debian supports unprivileged user namespaces, but
only if you turn on the kernel.unprivileged_userns_clone sysctl.

If unprivileged user namespaces is not available, then bubblewrap must
be built as setuid root. This is believed to be safe, as it is
designed to do this. Any build of bubblewrap supports both
unprivileged and setuid mode, you just need to set the setuid bit for
it to change mode.

However, this it does complicate the installation a bit. If you pass
`--with-priv-mode=setuid` to configure (of flatpak or bubblewrap) then
make install will try to set the setuid bit. However that means you
have to run make install as root. Alternatively, you can pass
`--enable-sudo` to configure and it will call sudo when setting the
setuid bit. Alternatively you can enable setuid completely outside of
the installation, which is common for example when packaging bubblewrap
in a .deb or .rpm.

There are some complications when building flatpak to a different
prefix than the system-installed version. First of all, the newly
built flatpak will look for system-installed flatpaks in
`$PREFIX/var/lib/flatpak`, which will not match existing installed
flatpaks. You can use `--with-system-install-dir=/var/lib/flatpak`
to make both installations use the same location.

Secondly, flatpak ships with a root-privileged policykit helper for
system-installation, called flatpak-system-helper. This is dbus
activated (on the system-bus) and if you install in a non-standard
location it is likely that this will not be found by dbus and
policykit. However, if the system installation is synchronized it
you can often use the system installed helper instead. At least
if the two versions are close in versions.
