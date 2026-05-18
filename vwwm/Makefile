.POSIX:
.SUFFIXES:

include config.mk

# flags for compiling
DWLCPPFLAGS = -I. -DWLR_USE_UNSTABLE -D_POSIX_C_SOURCE=200809L \
	-DVERSION=\"$(VERSION)\" $(XWAYLAND)
DWLDEVCFLAGS = -g -Wpedantic -Wall -Wextra -Wdeclaration-after-statement \
	-Wno-unused-parameter -Wshadow -Wunused-macros -Werror=strict-prototypes \
	-Werror=implicit -Werror=return-type -Werror=incompatible-pointer-types \
	-Wfloat-conversion

# CFLAGS / LDFLAGS
PKGS      = wayland-server xkbcommon libinput $(XLIBS)
DWLCFLAGS = `$(PKG_CONFIG) --cflags $(PKGS)` $(WLR_INCS) $(DWLCPPFLAGS) $(DWLDEVCFLAGS) $(CFLAGS)
LDLIBS    = `$(PKG_CONFIG) --libs $(PKGS)` $(WLR_LIBS) -lm $(LIBS)

all: vwwm
vwwm: vwwm.o util.o
	$(CC) vwwm.o util.o $(DWLCFLAGS) $(LDFLAGS) $(LDLIBS) -o $@
vwwm.o: vwwm.c client.h config.h config.mk cursor-shape-v1-protocol.h \
	pointer-constraints-unstable-v1-protocol.h wlr-layer-shell-unstable-v1-protocol.h \
	wlr-output-power-management-unstable-v1-protocol.h xdg-shell-protocol.h
util.o: util.c util.h

# wayland-scanner is a tool which generates C headers and rigging for Wayland
# protocols, which are specified in XML. wlroots requires you to rig these up
# to your build system yourself and provide them in the include path.
WAYLAND_SCANNER   = `$(PKG_CONFIG) --variable=wayland_scanner wayland-scanner`
WAYLAND_PROTOCOLS = `$(PKG_CONFIG) --variable=pkgdatadir wayland-protocols`

cursor-shape-v1-protocol.h:
	$(WAYLAND_SCANNER) enum-header \
		$(WAYLAND_PROTOCOLS)/staging/cursor-shape/cursor-shape-v1.xml $@
pointer-constraints-unstable-v1-protocol.h:
	$(WAYLAND_SCANNER) enum-header \
		$(WAYLAND_PROTOCOLS)/unstable/pointer-constraints/pointer-constraints-unstable-v1.xml $@
wlr-layer-shell-unstable-v1-protocol.h:
	$(WAYLAND_SCANNER) enum-header \
		protocols/wlr-layer-shell-unstable-v1.xml $@
wlr-output-power-management-unstable-v1-protocol.h:
	$(WAYLAND_SCANNER) server-header \
		protocols/wlr-output-power-management-unstable-v1.xml $@
xdg-shell-protocol.h:
	$(WAYLAND_SCANNER) server-header \
		$(WAYLAND_PROTOCOLS)/stable/xdg-shell/xdg-shell.xml $@

config.h: config.def.h
	cp -f config.def.h $@
clean:
	rm -f vwwm *.o *-protocol.h

dist: clean
	mkdir -p vwwm-$(VERSION)
	cp -R LICENSE* Makefile CHANGELOG.md README.md client.h config.def.h \
		config.mk protocols vwwm.1 vwwm.c util.c util.h vwwm.desktop \
		vwwm-$(VERSION)
	tar -caf vwwm-$(VERSION).tar.gz vwwm-$(VERSION)
	rm -rf vwwm-$(VERSION)

install: vwwm
	mkdir -p $(DESTDIR)$(PREFIX)/bin
	rm -f $(DESTDIR)$(PREFIX)/bin/vwwm
	cp -f vwwm $(DESTDIR)$(PREFIX)/bin
	chmod 755 $(DESTDIR)$(PREFIX)/bin/vwwm
	mkdir -p $(DESTDIR)$(MANDIR)/man1
	cp -f vwwm.1 $(DESTDIR)$(MANDIR)/man1
	chmod 644 $(DESTDIR)$(MANDIR)/man1/vwwm.1
	mkdir -p $(DESTDIR)$(DATADIR)/wayland-sessions
	cp -f vwwm.desktop $(DESTDIR)$(DATADIR)/wayland-sessions/vwwm.desktop
	chmod 644 $(DESTDIR)$(DATADIR)/wayland-sessions/vwwm.desktop
uninstall:
	rm -f $(DESTDIR)$(PREFIX)/bin/vwwm $(DESTDIR)$(MANDIR)/man1/vwwm.1 \
		$(DESTDIR)$(DATADIR)/wayland-sessions/vwwm.desktop

.SUFFIXES: .c .o
.c.o:
	$(CC) $(CPPFLAGS) $(DWLCFLAGS) -o $@ -c $<
