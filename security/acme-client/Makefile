# Created by: Bernard Spil <brnrd@FreeBSD.org>
# $FreeBSD$

PORTNAME=	acme-client
PORTVERSION=	0.1.16
PORTREVISION=	1
DISTVERSIONPREFIX=	portable-
CATEGORIES=	security
MASTER_SITES=	https://kristaps.bsd.lv/${PORTNAME}/snapshots/ \
		LOCAL/brnrd

MAINTAINER=	brnrd@FreeBSD.org
COMMENT=	Native C client for Let's Encrypt, designed for security

LICENSE=	ISCL

RUN_DEPENDS=	${LOCALBASE}/share/certs/ca-root-nss.crt:security/ca_root_nss

USES=		gmake tar:tgz ssl

MAKEFILE=	GNUmakefile
MAKE_ENV=	PREFIX=${STAGEDIR}/${PREFIX}

WWWDIR=		${PREFIX}/www/acme

SAMPLE_FILES=	acme-client.sh.sample deploy.sh.sample
SUB_FILES=	000.acme-client.sh pkg-message ${SAMPLE_FILES}
SUB_LIST=	PORTNAME=${PORTNAME}

PERIODIC_DIRS=	etc/periodic/weekly
PERIODIC_FILES=	000.acme-client.sh

post-patch:
	${REINPLACE_CMD} -e "s|/etc/|${PREFIX}/etc/|g" \
		-e "s|/var/www/acme|${WWWDIR}|" \
		${WRKSRC}/main.c ${WRKSRC}/acme-client.1

post-install:
	${MKDIR} ${STAGEDIR}${PREFIX}/${PERIODIC_DIRS}
	${INSTALL_SCRIPT} ${WRKDIR}/${PERIODIC_FILES} ${STAGEDIR}${PREFIX}/${PERIODIC_DIRS}/${PERIODIC_FILES}
	${STRIP_CMD} ${STAGEDIR}${PREFIX}/bin/acme-client
.	for d in etc/ssl/acme etc/ssl/acme/private \
		etc/acme www/acme
		${MKDIR} ${STAGEDIR}${PREFIX}/${d}
.	endfor
.	for d in ${SAMPLE_FILES}
		${INSTALL_SCRIPT} ${WRKDIR}/${d} ${STAGEDIR}${PREFIX}/etc/acme/${d}
.	endfor

.include <bsd.port.pre.mk>

.if ! ${SSL_DEFAULT:Mlibressl*}
BUILD_DEPENDS+=		${NONEXISTENT}:security/libressl:stage
CPPFLAGS+=		-I${WRKDIR}/libressl/include
LDFLAGS+=		-L${WRKDIR}/libressl/lib
. ifnmake describe
STAGEDIR_libressl!=	${MAKE} -V STAGEDIR -C ${PORTSDIR}/security/libressl
. endif

pre-configure:
	@(cd ${STAGEDIR_libressl}${LOCALBASE} && ${COPYTREE_SHARE} \
		. ${WRKDIR}/libressl "! -name *.so*")
.endif # SSL_DEFAULT

.include <bsd.port.post.mk>
