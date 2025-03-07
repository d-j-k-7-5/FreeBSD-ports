# Created by: Bernhard Froehlich <decke@bluelife.at>

PORTNAME=	virtualbox-ose
PORTVERSION=	6.1.26
CATEGORIES=	emulators
MASTER_SITES=	https://download.virtualbox.org/virtualbox/${PORTVERSION}/
DISTFILES=	VirtualBox-${PORTVERSION}${EXTRACT_SUFX} ${GUESTADDITIONS}
EXTRACT_ONLY=	VirtualBox-${PORTVERSION}${EXTRACT_SUFX}

MAINTAINER=	vbox@FreeBSD.org
COMMENT=	General-purpose full virtualizer for x86 hardware

LICENSE=	GPLv2
LICENSE_FILE=	${WRKSRC}/COPYING

BUILD_DEPENDS=	yasm:devel/yasm \
		xsltproc:textproc/libxslt \
		kmk:devel/kBuild \
		gtar:archivers/gtar
LIB_DEPENDS=	libpng.so:graphics/png \
		libxslt.so:textproc/libxslt \
		libcurl.so:ftp/curl
RUN_DEPENDS=	${LOCALBASE}/etc/rc.d/vboxnet:emulators/virtualbox-ose-kmod

CPE_VENDOR=	oracle
CPE_PRODUCT=	vm_virtualbox

WRKSRC=		${WRKDIR}/VirtualBox-${PORTVERSION}
ONLY_FOR_ARCHS=	amd64
USES=		compiler:c++14-lang cpe gnome iconv pkgconfig ssl tar:bzip2
USE_GNOME=	libidl libxml2

HAS_CONFIGURE=	yes
CONFIGURE_ARGS=	--disable-java --passive-mesa
CONFIGURE_ARGS+=	--with-gcc="${CC}" --with-g++="${CXX}"

CONFLICTS_INSTALL=	virtualbox-ose-additions-* \
			virtualbox-ose-devel-[0-9]* \
			virtualbox-ose-legacy-[0-9]* \
			virtualbox-ose-lite-[0-9]*

VBOXUSER?=	vboxusers
VBOXWSUSER?=	vboxusers
VBOXGROUP?=	vboxusers
USERS=		${VBOXUSER}
GROUPS=		${VBOXGROUP}
VBOX_DIR=	${PREFIX}/${VBOX_DIR_REL}
VBOX_DIR_REL=	lib/virtualbox
VBOX_LINKS=	VBoxVRDP
VBOX_PROGS=	VBoxAutostart VBoxBalloonCtrl VBoxBugReport VBoxHeadless \
		VBoxManage
VBOX_UTILS=	VBoxExtPackHelperApp VBoxNetAdpCtl VBoxNetDHCP VBoxNetNAT \
		VBoxSVC VBoxXPCOMIPCD

OPTIONS_DEFINE=	AIO ALSA DBUS DEBUG GUESTADDITIONS MANUAL NLS OPUS PULSEAUDIO \
		PYTHON QT5 R0LOGGING UDPTUNNEL VDE VNC WEBSERVICE VPX X11
OPTIONS_DEFAULT=	AIO DBUS QT5 UDPTUNNEL VNC WEBSERVICE X11
# Since version 6.1.24 pulseaudio is broken at runtime, preventing
# virtual machines from starting if configured to use it.
OPTIONS_EXCLUDE=	PULSEAUDIO
OPTIONS_SUB=	yes

AIO_DESC=	Enable Asyncronous IO support (check pkg-message)
DEBUG_DESC=	Debug symbols, additional logs and assertions
GUESTADDITIONS_DESC=	Build with Guest Additions
MANUAL_DESC=	Build with user manual
NLS_DESC=	Native language support (requires QT5)
OPUS_DESC=	Use libvpx for audio recording (requires VPX)
QT5_DESC=	Build with QT5 frontend (requires X11)
R0LOGGING_DESC=	Enable R0 logging (requires DEBUG)
UDPTUNNEL_DESC=	Build with UDP tunnel support
VDE_DESC=	Build with VDE support
VNC_DESC=	Build with VNC support
VPX_DESC=	Use libvpx for video recording
WEBSERVICE_DESC=	Build Webservice

ALSA_CONFIGURE_OFF=	--disable-alsa
ALSA_LIB_DEPENDS=	libasound.so:audio/alsa-lib
DBUS_CONFIGURE_OFF=	--disable-dbus
DBUS_LIB_DEPENDS=	libdbus-1.so:devel/dbus
DEBUG_CONFIGURE_ON=	--build-debug
MANUAL_BUILD_DEPENDS=	${LOCALBASE}/share/xml/docbook/4.4/docbookx.dtd:textproc/docbook-xml
MANUAL_CONFIGURE_OFF=	--disable-docs
NLS_IMPLIES=		QT5
OPUS_CONFIGURE_OFF=	--disable-libopus
OPUS_LIB_DEPENDS=	libopus.so:audio/opus
OPUS_IMPLIES=		VPX
PULSEAUDIO_CONFIGURE_OFF=	--disable-pulse
PULSEAUDIO_LIB_DEPENDS=	libpulse.so:audio/pulseaudio
PYTHON_CONFIGURE_OFF=	--disable-python
QT5_IMPLIES=		X11
R0LOGGING_IMPLIES=	DEBUG
UDPTUNNEL_CONFIGURE_OFF=	--disable-udptunnel
VDE_CONFIGURE_ON=	--enable-vde
VDE_RUN_DEPENDS=	vde_switch:net/vde2
VNC_CONFIGURE_ON=	--enable-vnc
VNC_LIB_DEPENDS=	libvncserver.so:net/libvncserver
VPX_CONFIGURE_OFF=	--disable-libvpx
VPX_LIB_DEPENDS=	libvpx.so:multimedia/libvpx
X11_CONFIGURE_OFF=	--build-headless

ENV=
.export ENV

PLIST_SUB=	GUEST_VER=${PORTVERSION} \
		PYTHON_VERU=${PYTHON_VER:S/./_/}${PYTHON_ABIVER} \
		VBOXGROUP=${VBOXGROUP}
SUB_LIST=	VBOXDIR=${VBOX_DIR} \
		VBOXGROUP=${VBOXGROUP} \
		VBOXUSER=${VBOXUSER} \
		VBOXWSUSER=${VBOXWSUSER}
USE_RC_SUBR=	vboxheadless vboxwatchdog

.include <bsd.port.options.mk>

.if ${SLAVE_PORT} == no
CONFLICTS_INSTALL+=	virtualbox-ose-nox11-[0-9]*
OPTIONS_DEFAULT+=	PYTHON
.else
CONFLICTS_INSTALL+=	virtualbox-ose-[0-9]*
.endif

.if ${ARCH} == "amd64"
_ELF32!=	kldstat -q -m elf32 && echo yes || echo no
.endif

.if ${PORT_OPTIONS:MDEBUG}
KMK_BUILDTYPE=	debug
KMK_FLAGS+=	BUILD_TYPE=debug
WITH_DEBUG=	yes
.else
KMK_BUILDTYPE=	release
.endif

.if ${PORT_OPTIONS:MGUESTADDITIONS}
GUESTADDITIONS=	VBoxGuestAdditions_${PORTVERSION}.iso
LICENSE+=	Additions
LICENSE_COMB=	multi
LICENSE_NAME_Additions=	Guest Additions
LICENSE_PERMS_Additions=	auto-accept
LICENSE_DISTFILES_Additions=	${GUESTADDITIONS}
.endif

.if ${PORT_OPTIONS:MMANUAL}
DBKXMLDIR=	${LOCALBASE}/share/xml/docbook/4.4
DBKXSLDIR=	${LOCALBASE}/share/xsl/docbook
USE_TEX=	dvipsk:build formats:build
.endif

.if ${PORT_OPTIONS:MPYTHON}
USES+=		python
USE_PYTHON=	distutils noegginfo noflavors
PYDISTUTILS_PKGNAME=	vboxapi
PYDISTUTILS_PKGVERSION=	1.0
.else
USES+=		python:build
.endif

.if ${PORT_OPTIONS:MQT5}
CONFIGURE_ARGS+=	--enable-qt5
PLIST_SUB+=	QT=""
USES+=		gl qmake:no_env qt:5 xorg
USE_GL=		gl glu
USE_QT=		buildtools_build core dbus gui linguisttools_build opengl
USE_QT+=	printsupport widgets x11extras
USE_XORG=	xcb
INSTALLS_ICONS=	yes
VBOX_PROGS+=	VirtualBox VirtualBoxVM
VBOX_UTILS+=	VBoxTestOGL
VBOX_WITH_QT=	1
.else
CONFIGURE_ARGS+=	--disable-qt
PLIST_SUB+=	QT="@comment "
.endif

.if ${PORT_OPTIONS:MWEBSERVICE}
BUILD_DEPENDS+=	soapcpp2:devel/gsoap
USE_RC_SUBR+=	vboxwebsrv
VBOX_LINKS+=	vboxwebsrv
VBOX_UTILS+=	vboxwebsrv webtest
.endif

.if ${PORT_OPTIONS:MX11}
USES+=		sdl xorg
USE_SDL=	sdl
USE_XORG+=	xorgproto x11 xcursor xext xinerama xmu xt
VBOX_PROGS+=	VBoxSDL
.endif

.if ${PORT_OPTIONS:MPYTHON} || ${PORT_OPTIONS:MWEBSERVICE}
PLIST_SUB+=	SDK=""
.else
PLIST_SUB+=	SDK="@comment "
.endif

.if ${ARCH} == i386
KMK_ARCH=	freebsd.x86
PLIST_SUB+=	I386=""
.else
KMK_ARCH=	freebsd.${ARCH}
PLIST_SUB+=	I386="@comment "
.endif

PLIST_SUB+=	ARCH="${KMK_ARCH}"

KMK_BUILDDIR=	${WRKSRC}/out/${KMK_ARCH}/${KMK_BUILDTYPE}
KMK_CONFIG=	VBOX_LIBPATH_X11=${LOCALBASE} VBOX_FREEBSD_SRC=${SRC_BASE}/sys
KMK_FLAGS+=	-j${MAKE_JOBS_NUMBER}

.include <bsd.port.pre.mk>

.if ${CHOSEN_COMPILER_TYPE} == clang
# llvm10 in FreeBSD before r364284 miscompiles virtualbox 6.1 causing errors.
# force llvm11 from ports
.if ${OPSYS} == FreeBSD && ${OSVERSION} < 1300109
BUILD_DEPENDS+=	clang11:devel/llvm${VBOX_LLVM_VER}
CC=		${LOCALBASE}/bin/clang${VBOX_LLVM_VER}
CXX=		${LOCALBASE}/bin/clang++${VBOX_LLVM_VER}
VBOX_LLVM_VER?=	11
.endif
PATCH_DEPENDS+=	${LOCALBASE}/share/kBuild/tools/GXX3.kmk:devel/kBuild
EXTRA_PATCHES+=	${PATCHDIR}/extrapatch-src-VBox-Devices-PC-ipxe-Makefile.kmk
.endif

.if ${PYTHON_MAJOR_VER} >= 3
PLIST_SUB+=	PYTHON_PYCDIR=/__pycache__/ \
		PYTHON_PYCEXT=.cpython-${PYTHON_SUFFIX}.pyc
.else
PLIST_SUB+=	PYTHON_PYCDIR=/ \
		PYTHON_PYCEXT=.pyc
.endif

.if ${SSL_DEFAULT} != base
CONFIGURE_ARGS+=	--with-openssl-dir="${OPENSSLBASE}"
.endif

pre-everything::
.if ${ARCH} == "amd64"
.if ${_ELF32} != yes
	@${ECHO_MSG} 'Requires 32-bit runtime support in kernel.'
	@${ECHO_MSG} 'Rebuild kernel with "options COMPAT_FREEBSD32" and reboot.'
	@${FALSE}
.elif !exists(/usr/lib32/libc.so)
	@${ECHO_MSG} 'Requires 32-bit libraries installed under /usr/lib32.'
	@${ECHO_MSG} 'Do: cd /usr/src; make build32 install32; service ldconfig restart'
	@${FALSE}
.endif
.endif

post-patch:
	@${ECHO_CMD} 'VBOX_PATH_APP_PRIVATE_ARCH = ${VBOX_DIR}' > \
	    ${WRKSRC}/LocalConfig.kmk
	@${ECHO_CMD} 'VBOX_PATH_DOCBOOK = ${DBKXSLDIR}' >> \
	    ${WRKSRC}/LocalConfig.kmk
	@${ECHO_CMD} 'VBOX_PATH_DOCBOOK_DTD = ${DBKXMLDIR}' >> \
	    ${WRKSRC}/LocalConfig.kmk
	@${ECHO_CMD} 'VBOX_PATH_SHARED_LIBS = ${VBOX_DIR}' >> \
	    ${WRKSRC}/LocalConfig.kmk
	# Please keep this even if using Clang to avoid repeated regressions.
	# PR 245048
	@${ECHO_CMD} "VBOX_WITH_RUNPATH = ${_GCC_RUNTIME:D${_GCC_RUNTIME}\:}${VBOX_DIR}" >> \
	    ${WRKSRC}/LocalConfig.kmk
	@${ECHO_CMD} 'VBOX_PATH_APP_PRIVATE = ${DATADIR}' >> \
	    ${WRKSRC}/LocalConfig.kmk
	@${ECHO_CMD} 'VBOX_PATH_APP_DOCS = ${DOCSDIR}' >> ${WRKSRC}/LocalConfig.kmk
.if ${SSL_DEFAULT} != base
	@${ECHO_CMD} 'VBOX_WITH_ALT_HASH_CODE = 1' >> ${WRKSRC}/LocalConfig.kmk
.endif
	@${ECHO_CMD} 'VBOX_WITH_EXTPACK_VBOXDTRACE =' >> ${WRKSRC}/LocalConfig.kmk
	@${ECHO_CMD} 'VBOX_WITH_INSTALLER = 1' >> ${WRKSRC}/LocalConfig.kmk
	@${ECHO_CMD} 'VBOX_WITH_VBOXDRV =' >> ${WRKSRC}/LocalConfig.kmk
	@${ECHO_CMD} 'VBOX_WITH_TESTCASES =' >> ${WRKSRC}/LocalConfig.kmk
	@${ECHO_CMD} 'SDK_VBOX_LIBPNG_INCS = ${PREFIX}/include/libpng' >> \
	    ${WRKSRC}/LocalConfig.kmk
	@${ECHO_CMD} 'SDK_VBOX_LIBPNG_LIBS = png' >> ${WRKSRC}/LocalConfig.kmk
	@${ECHO_CMD} 'VBOX_WITH_ADDITIONS =' >> ${WRKSRC}/LocalConfig.kmk
	@${ECHO_CMD} 'VBOX_WITH_DRAG_AND_DROP = ${VBOX_WITH_QT}' >> \
	    ${WRKSRC}/LocalConfig.kmk
	@${ECHO_CMD} 'VBOX_WITH_DRAG_AND_DROP_GH = ${VBOX_WITH_QT}' >> \
	    ${WRKSRC}/LocalConfig.kmk
	@${ECHO_CMD} 'VBOX_WITH_VALIDATIONKIT =' >> ${WRKSRC}/LocalConfig.kmk
	@${ECHO_CMD} 'VBOX_WITH_X11_ADDITIONS =' >> ${WRKSRC}/LocalConfig.kmk
.if ${PORT_OPTIONS:MR0LOGGING}
	@${ECHO_CMD} 'VBOX_WITH_R0_LOGGING = 1' >> ${WRKSRC}/LocalConfig.kmk
.endif
.if ${PORT_OPTIONS:MWEBSERVICE}
	@${ECHO_CMD} 'VBOX_WITH_WEBSERVICES = 1' >> ${WRKSRC}/LocalConfig.kmk
	@${ECHO_CMD} 'VBOX_GSOAP_INSTALLED = 1' >> ${WRKSRC}/LocalConfig.kmk
	@${ECHO_CMD} 'VBOX_PATH_GSOAP = ${PREFIX}/lib/gsoap' >> \
	    ${WRKSRC}/LocalConfig.kmk
	@${ECHO_CMD} 'VBOX_PATH_GSOAP_BIN = ${PREFIX}/bin' >> \
	    ${WRKSRC}/LocalConfig.kmk
	@${ECHO_CMD} 'VBOX_PATH_GSOAP_IMPORT = ${PREFIX}/share/gsoap/import' >> \
	    ${WRKSRC}/LocalConfig.kmk
	@${ECHO_CMD} 'VBOX_GCC_PEDANTIC_CXX = -Wshadow $$(VBOX_GCC_WARN) -Wno-long-long' >> \
	    ${WRKSRC}/LocalConfig.kmk
.endif
.if empty(PORT_OPTIONS:MX11)
	@${ECHO} 'VBOX_WITH_VMSVGA3D =' >> ${WRKSRC}/LocalConfig.kmk
.endif
.if ${CHOSEN_COMPILER_TYPE} == clang
	@${REINPLACE_CMD} -e 's| -finline-limit=8000||' \
	    -e 's| -mpreferred-stack-boundary=2||' ${WRKSRC}/Config.kmk
	@${REINPLACE_CMD} -e 's| -fpermissive||' ${WRKSRC}/Config.kmk \
	    ${WRKSRC}/src/VBox/Main/webservice/Makefile.kmk
	@${ECHO_CMD} 'TOOL_VBoxGccFreeBSD_LD = ${CXX}' >> ${WRKSRC}/LocalConfig.kmk
	@${SED} -e 's|GXX3|VBoxGccFreeBSD|g' \
	    ${LOCALBASE}/share/kBuild/tools/GXX3.kmk > \
	    ${WRKSRC}/tools/kBuildTools/VBoxGccFreeBSD.kmk
.endif
	@${REINPLACE_CMD} -e 's|/usr/local|${LOCALBASE}|g' \
	    ${WRKSRC}/Config.kmk ${WRKSRC}/configure \
	    ${WRKSRC}/kBuild/header.kmk ${WRKSRC}/kBuild/units/qt4.kmk \
	    ${WRKSRC}/kBuild/units/qt5.kmk ${WRKSRC}/kBuild/sdks/LIBSDL.kmk \
	    ${WRKSRC}/src/libs/xpcom18a4/python/gen_python_deps.py
	@${REINPLACE_CMD} \
	    -e 's|\$$KBUILDDIR_BIN/kmk_sed|${LOCALBASE}/bin/kmk_sed|g' \
	    -e 's|SUPPYTHONLIBS=.*|SUPPYTHONLIBS="${PYTHON_VERSION}${PYTHON_ABIVER}"|' \
	    ${WRKSRC}/configure
.if empty(ICONV_LIB)
	@${REINPLACE_CMD} -e 's|iconv||' ${WRKSRC}/Config.kmk \
	    ${WRKSRC}/src/VBox/Runtime/Makefile.kmk
	@${ECHO_CMD} 'VBOX_ICONV_DEFS = LIBICONV_PLUG' >> ${WRKSRC}/LocalConfig.kmk
.endif
	@${REINPLACE_CMD} -e 's|/usr/local/lib/virtualbox|${VBOX_DIR}|' \
	    -e 's|/usr/local|${PREFIX}|' \
	    ${WRKSRC}/src/VBox/Installer/freebsd/VBox.sh
	@${REINPLACE_CMD} \
	    -e 's|^versions =.*|versions = ["${PYTHON_VER}${PYTHON_ABIVER}"]|' \
	    ${WRKSRC}/src/libs/xpcom18a4/python/gen_python_deps.py

post-patch-AIO-off:
	@${REINPLACE_CMD} 's|r3/freebsd/fileaio-freebsd.cpp|r3/posix/fileaio-posix.cpp|' \
		${WRKSRC}/src/VBox/Runtime/Makefile.kmk

do-build:
	cd ${WRKSRC} && ${SH} -c '. ${WRKSRC}/env.sh && \
	    ${KMK_CONFIG} ${LOCALBASE}/bin/kmk ${KMK_FLAGS}'
.if ${PORT_OPTIONS:MPYTHON}
	${PYTHON_CMD} -mcompileall \
	    ${KMK_BUILDDIR}/bin/sdk/bindings/xpcom/python/xpcom
.endif

do-install:
.if ${PORT_OPTIONS:MPYTHON} || ${PORT_OPTIONS:MWEBSERVICE}
	${MKDIR} ${STAGEDIR}${DATADIR}/sdk
.endif
	cd ${KMK_BUILDDIR}/bin/sdk/bindings/xpcom && \
	    ${COPYTREE_SHARE} "idl samples" ${STAGEDIR}${DATADIR}

	${MKDIR} ${STAGEDIR}${PREFIX}/include/virtualbox
	cd ${KMK_BUILDDIR}/bin/sdk/bindings/xpcom/include && \
	    ${COPYTREE_SHARE} "*" ${STAGEDIR}${PREFIX}/include/virtualbox

	${MKDIR} ${STAGEDIR}${VBOX_DIR}
	cd ${KMK_BUILDDIR}/bin && ${COPYTREE_SHARE} \
	    "*.fd *.r0 *.so components" ${STAGEDIR}${VBOX_DIR}
.if ${PORT_OPTIONS:MPYTHON} || ${PORT_OPTIONS:MWEBSERVICE}
	${RLN} ${STAGEDIR}${DATADIR}/sdk ${STAGEDIR}${VBOX_DIR}
.endif

	${INSTALL_SCRIPT} ${WRKSRC}/src/VBox/Installer/freebsd/VBox.sh \
	    ${STAGEDIR}${VBOX_DIR}
.for f in ${VBOX_PROGS} ${VBOX_UTILS}
	${INSTALL_PROGRAM} ${KMK_BUILDDIR}/bin/${f} ${STAGEDIR}${VBOX_DIR}
.endfor
.for f in ${VBOX_PROGS} ${VBOX_LINKS}
	${LN} -fs ../${VBOX_DIR_REL}/VBox.sh ${STAGEDIR}${PREFIX}/bin/${f}
.endfor
.for f in ${VBOX_PROGS}
	${LN} -fs ../${VBOX_DIR_REL}/VBox.sh ${STAGEDIR}${PREFIX}/bin/${f:tl}
.endfor

.if ${PORT_OPTIONS:MGUESTADDITIONS}
	${MKDIR} ${STAGEDIR}${VBOX_DIR}/additions
	${INSTALL_DATA} ${DISTDIR}/${GUESTADDITIONS} \
	    ${STAGEDIR}${VBOX_DIR}/additions/
	${RLN} ${STAGEDIR}${VBOX_DIR}/additions/${GUESTADDITIONS} \
	    ${STAGEDIR}${VBOX_DIR}/additions/VBoxGuestAdditions.iso
.endif

.if ${PORT_OPTIONS:MMANUAL}
	${MKDIR} ${STAGEDIR}${DOCSDIR}
	${INSTALL_DATA} ${KMK_BUILDDIR}/bin/UserManual*.pdf \
	    ${STAGEDIR}${DOCSDIR}
.endif

.if ${PORT_OPTIONS:MNLS}
	cd ${KMK_BUILDDIR}/obj/VirtualBox/qtnls && \
	    ${COPYTREE_SHARE} "*.qm" ${STAGEDIR}${DATADIR}/nls
.endif

.if ${PORT_OPTIONS:MPYTHON}
	cd ${KMK_BUILDDIR}/bin/sdk/installer && \
	    ${SETENV} VBOX_INSTALL_PATH="${VBOX_DIR}" \
	    ${PYTHON_CMD} vboxapisetup.py install --root=${STAGEDIR}

	@${MKDIR} ${STAGEDIR}${PYTHON_SITELIBDIR}/xpcom
	cd ${KMK_BUILDDIR}/bin/sdk/bindings/xpcom/python/xpcom && \
	    ${COPYTREE_SHARE} "*" ${STAGEDIR}${PYTHON_SITELIBDIR}/xpcom
	@${MKDIR} ${STAGEDIR}${DATADIR}/sdk/bindings/xpcom/python
	${RLN} ${STAGEDIR}${PYTHON_SITELIBDIR}/xpcom \
	    ${STAGEDIR}${DATADIR}/sdk/bindings/xpcom/python
.endif

.if ${PORT_OPTIONS:MQT5}
	${INSTALL_DATA} \
	    ${WRKSRC}/src/VBox/Frontends/VirtualBox/images/OSE/VirtualBox_48px.png \
	    ${STAGEDIR}${PREFIX}/share/pixmaps/VBox.png
	${INSTALL_DATA} \
	    ${WRKSRC}/src/VBox/Installer/freebsd/virtualbox.desktop \
	    ${STAGEDIR}${PREFIX}/share/applications/virtualbox.desktop
.endif

.if ${PORT_OPTIONS:MVNC}
	${MKDIR} ${STAGEDIR}${VBOX_DIR}/ExtensionPacks/VNC/${KMK_ARCH}
	${INSTALL_DATA} ${KMK_BUILDDIR}/bin/ExtensionPacks/VNC/ExtPack* \
	    ${STAGEDIR}${VBOX_DIR}/ExtensionPacks/VNC/
	${INSTALL_LIB} ${KMK_BUILDDIR}/bin/ExtensionPacks/VNC/${KMK_ARCH}/* \
	    ${STAGEDIR}${VBOX_DIR}/ExtensionPacks/VNC/${KMK_ARCH}/
.endif

.if ${PORT_OPTIONS:MWEBSERVICE}
	${MKDIR} ${STAGEDIR}${DATADIR}/sdk/bindings/webservice
	${INSTALL_DATA} ${KMK_BUILDDIR}/obj/webservice/vboxweb.wsdl \
	    ${STAGEDIR}${DATADIR}/sdk/bindings/webservice/
.endif

post-install:
	${STRIP_CMD} ${STAGEDIR}${PREFIX}/lib/virtualbox/*.so \
		${STAGEDIR}${PREFIX}/lib/virtualbox/components/*.so

.include <bsd.port.post.mk>
