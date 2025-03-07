PORTNAME=	bindgen
DISTVERSION=	0.59.1
CATEGORIES=	devel
MASTER_SITES=	CRATESIO
PKGNAMEPREFIX=	rust-
# XXX Teach USES=cargo to have proper default
DISTFILES=	${CARGO_DIST_SUBDIR}/${DISTNAME}${CARGO_CRATE_EXT}

MAINTAINER=	gecko@FreeBSD.org
COMMENT=	Generate Rust bindings from C (and some C++) code

LICENSE=	BSD3CLAUSE
LICENSE_FILE=	${WRKSRC}/LICENSE

RUN_DEPENDS=	llvm${LLVM_DEFAULT}>0:devel/llvm${LLVM_DEFAULT}

USES=		cargo
PLIST_FILES=	bin/${PORTNAME}

CARGO_CRATES=	aho-corasick-0.7.15 \
		ansi_term-0.11.0 \
		atty-0.2.14 \
		bitflags-1.2.1 \
		bitvec-0.19.5 \
		cexpr-0.5.0 \
		cfg-if-0.1.10 \
		cfg-if-1.0.0 \
		clang-sys-1.0.3 \
		clap-2.33.3 \
		diff-0.1.12 \
		env_logger-0.8.1 \
		funty-1.1.0 \
		getrandom-0.2.2 \
		glob-0.3.0 \
		hermit-abi-0.1.17 \
		humantime-2.0.1 \
		lazy_static-1.4.0 \
		lazycell-1.3.0 \
		libc-0.2.80 \
		libloading-0.6.5 \
		log-0.4.11 \
		memchr-2.3.4 \
		nom-6.2.1 \
		peeking_take_while-0.1.2 \
		ppv-lite86-0.2.10 \
		proc-macro2-1.0.24 \
		quote-1.0.7 \
		radium-0.5.3 \
		rand-0.8.3 \
		rand_chacha-0.3.0 \
		rand_core-0.6.2 \
		rand_hc-0.3.0 \
		redox_syscall-0.2.5 \
		regex-1.4.2 \
		regex-syntax-0.6.21 \
		remove_dir_all-0.5.3 \
		rustc-hash-1.1.0 \
		shlex-1.0.0 \
		strsim-0.8.0 \
		tap-1.0.1 \
		tempfile-3.2.0 \
		termcolor-1.1.0 \
		textwrap-0.11.0 \
		thread_local-1.0.1 \
		unicode-width-0.1.8 \
		unicode-xid-0.2.1 \
		vec_map-0.8.2 \
		version_check-0.9.2 \
		wasi-0.10.2+wasi-snapshot-preview1 \
		which-3.1.1 \
		winapi-0.3.9 \
		winapi-i686-pc-windows-gnu-0.4.0 \
		winapi-util-0.1.5 \
		winapi-x86_64-pc-windows-gnu-0.4.0 \
		wyz-0.2.0

post-patch:
	@${REINPLACE_CMD} -e 's,"llvm-config,&${LLVM_DEFAULT},' \
		${WRKSRC}/cargo-crates/clang-sys-*/build/common.rs \
		${WRKSRC}/cargo-crates/clang-sys-*/src/support.rs

.include <bsd.port.mk>
