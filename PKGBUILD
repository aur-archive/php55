# $Id: PKGBUILD 185573 2013-05-15 08:05:33Z bpiotrowski $
# Maintainer: Pierre Schmitz <pierre@archlinux.de>
pkgname=php55

true && pkgbase=php55
true && pkgname=('php55'
#         'php55-cgi'
#         'php55-apache'
         'php55-fpm'
#         'php55-embed'
         'php55-pear'
#         'php55-enchant'
         'php55-gd'
         'php55-intl'
#         'php55-ldap'
         'php55-mcrypt'
#         'php55-mssql'
#         'php55-odbc'
#         'php55-pgsql'
#         'php55-pspell'
#         'php55-snmp'
         'php55-sqlite'
         'php55-tidy'
         'php55-xsl')
pkgver=5.5_20130620
pkgrel=2
arch=('i686' 'x86_64')
license=('PHP')
url='http://www.php.net'
makedepends=('apache' 'imap' 'postgresql-libs' 'libldap' 'postfix' 'libvpx'
             'sqlite' 'unixodbc' 'net-snmp' 'libzip' 'enchant' 'file' 'freetds'
             'libmcrypt' 'tidyhtml' 'aspell' 'libltdl' 'libpng' 'libjpeg' 'icu'
             'curl' 'libxslt' 'openssl' 'bzip2' 'db' 'gmp' 'freetype2')
source=("${pkgbase}-${pkgver}.tar.gz::http://snaps.php.net/php5.5-201306201030.tar.bz2"
        'php.ini.patch' 'apache.conf' 'php-fpm.conf.in.patch'
        'logrotate.d.php-fpm' 'php-fpm.service' 'php-fpm.tmpfiles')
md5sums=('2d522cf87d4ab5fdbcc9a33c84a07599'
         '44c1bb1df346b3b9d3e092c7df9a37d0'
         'dec2cbaad64e3abf4f0ec70e1de4e8e9'
         '5c1f4c967b66ce3eaf819b46951dcbf5'
         'e036c721e5ad927cd1613c7475a58b3a'
         '4af4d94f3db58437eef8db87d16a5fab'
         'c60343df74f8e1afb13b084d5c0e47ed')

prepare() {
	tmpsrcdir=`ls | grep php5.5 | head -n1`
	mv ${srcdir}/${tmpsrcdir} ${srcdir}/${pkgbase}-${pkgver}
	cd ${srcdir}/${pkgbase}-${pkgver}

	patch -p0 -i ${srcdir}/php.ini.patch
	patch -p0 -i ${srcdir}/php-fpm.conf.in.patch
}

build() {
	local _phpconfig="--srcdir=../${pkgbase}-${pkgver} \
		--config-cache \
		--prefix=/usr \
		--sbindir=/usr/bin \
		--sysconfdir=/etc/php \
		--localstatedir=/var \
		--with-layout=GNU \
		--with-config-file-path=/etc/php \
		--with-config-file-scan-dir=/etc/php/conf.d \
		--disable-rpath \
		--mandir=/usr/share/man \
		--without-pear \
		"

	local _phpextensions="--enable-bcmath=shared \
		--enable-calendar=shared \
		--enable-dba=shared \
		--enable-exif=shared \
		--enable-ftp=shared \
		--enable-gd-native-ttf \
		--enable-intl=shared \
		--enable-mbstring \
		--enable-phar=shared \
		--enable-posix=shared \
		--enable-shmop=shared \
		--enable-soap=shared \
		--enable-sockets=shared \
		--enable-sysvmsg=shared \
		--enable-sysvsem=shared \
		--enable-sysvshm=shared \
		--enable-zip=shared \
		--with-bz2=shared \
		--with-curl=shared \
		--with-db4=/usr \
		--with-enchant=shared,/usr \
		--with-freetype-dir=/usr \
		--with-gd=shared \
		--with-gdbm \
		--with-gettext=shared \
		--with-gmp=shared \
		--with-iconv=shared \
		--with-icu-dir=/usr \
		--with-imap-ssl \
		--with-imap=shared \
		--with-jpeg-dir=/usr \
		--with-vpx-dir=/usr \
		--with-ldap=shared \
		--with-ldap-sasl \
		--with-mcrypt=shared \
		--with-mhash \
		--with-mssql=shared \
		--with-mysql-sock=/var/run/mysqld/mysqld.sock \
		--with-mysql=shared,mysqlnd \
		--with-mysqli=shared,mysqlnd \
		--with-openssl=shared \
		--with-pcre-regex=/usr \
		--with-pdo-mysql=shared,mysqlnd \
		--with-pdo-odbc=shared,unixODBC,/usr \
		--with-pdo-pgsql=shared \
		--with-pdo-sqlite=shared,/usr \
		--with-pgsql=shared \
		--with-png-dir=/usr \
		--with-pspell=shared \
		--with-snmp=shared \
		--with-sqlite3=shared,/usr \
		--with-tidy=shared \
		--with-unixODBC=shared,/usr \
		--with-xmlrpc=shared \
		--with-xsl=shared \
		--with-zlib \
		"

	EXTENSION_DIR=/usr/lib/php/modules
	export EXTENSION_DIR
	PEAR_INSTALLDIR=/usr/share/pear
	export PEAR_INSTALLDIR

	cd ${srcdir}/${pkgbase}-${pkgver}

	# php
	mkdir ${srcdir}/build-php
	cd ${srcdir}/build-php
	ln -s ../${pkgbase}-${pkgver}/configure
	./configure ${_phpconfig} \
		--disable-cgi \
		--with-readline \
		--enable-pcntl \
		${_phpextensions}
	make

	# cgi and fcgi
	# reuse the previous run; this will save us a lot of time
	cp -a ${srcdir}/build-php ${srcdir}/build-cgi
	cd ${srcdir}/build-cgi
	./configure ${_phpconfig} \
		--disable-cli \
		--enable-cgi \
		${_phpextensions}
	make

	# apache
	cp -a ${srcdir}/build-php ${srcdir}/build-apache
	cd ${srcdir}/build-apache
	./configure ${_phpconfig} \
		--disable-cli \
		--with-apxs2 \
		${_phpextensions}
	make

	# fpm
	cp -a ${srcdir}/build-php ${srcdir}/build-fpm
	cd ${srcdir}/build-fpm
	./configure ${_phpconfig} \
		--disable-cli \
		--enable-fpm \
		--with-fpm-user=http \
		--with-fpm-group=http \
		${_phpextensions}
	make

	# embed
	cp -a ${srcdir}/build-php ${srcdir}/build-embed
	cd ${srcdir}/build-embed
	./configure ${_phpconfig} \
		--disable-cli \
		--enable-embed=shared \
		${_phpextensions}
	make

	# pear
	cp -a ${srcdir}/build-php ${srcdir}/build-pear
	cd ${srcdir}/build-pear
	./configure ${_phpconfig} \
		--disable-cgi \
		--with-readline \
		--enable-pcntl \
		--with-pear \
		${_phpextensions}
	make
}

# check() {
# 	cd ${srcdir}/build-php
# 	make test
# }

package_php55() {
	pkgdesc='An HTML-embedded scripting language'
	depends=('pcre' 'libxml2' 'bzip2' 'curl')
	replaces=('php-fileinfo' 'php-gmp' 'php-curl' 'php')
	provides=('php-fileinfo' 'php-gmp' 'php-curl' 'php')
	conflicts=('php-fileinfo' 'php-gmp' 'php-curl' 'php')
	backup=('etc/php/php.ini')

	cd ${srcdir}/build-php
	make -j1 INSTALL_ROOT=${pkgdir} install
	install -d -m755 ${pkgdir}/usr/share/pear
	# install php.ini
	install -D -m644 ${srcdir}/${pkgbase}-${pkgver}/php.ini-production ${pkgdir}/etc/php/php.ini
	install -d -m755 ${pkgdir}/etc/php/conf.d/

	# remove static modules
	rm -f ${pkgdir}/usr/lib/php/modules/*.a
	# remove modules provided by sub packages
	rm -f ${pkgdir}/usr/lib/php/modules/{enchant,gd,intl,ldap,mcrypt,mssql,odbc,pdo_odbc,pgsql,pdo_pgsql,pspell,snmp,sqlite3,pdo_sqlite,tidy,xsl}.so
	# remove empty directory
	rmdir ${pkgdir}/usr/include/php/include
}

package_php55-cgi() {
	pkgdesc='CGI and FCGI SAPI for PHP'
	depends=('php55')
        conflicts=('php-cgi')
	provides=('php-cgi')

	install -D -m755 ${srcdir}/build-cgi/sapi/cgi/php-cgi ${pkgdir}/usr/bin/php-cgi
}

package_php55-apache() {
	pkgdesc='Apache SAPI for PHP'
	depends=('php55' 'apache')
	conflicts=('php-apache')
	provides=('php-apache')
	backup=('etc/httpd/conf/extra/php5_module.conf')

	install -D -m755 ${srcdir}/build-apache/libs/libphp5.so ${pkgdir}/usr/lib/httpd/modules/libphp5.so
	install -D -m644 ${srcdir}/apache.conf ${pkgdir}/etc/httpd/conf/extra/php5_module.conf
}

package_php55-fpm() {
	pkgdesc='FastCGI Process Manager for PHP'
	depends=('php55' 'systemd')
	conflicts=('php-fpm')
	provides=('php-fpm')

	backup=('etc/php/php-fpm.conf')
	install='php-fpm.install'

	install -D -m755 ${srcdir}/build-fpm/sapi/fpm/php-fpm ${pkgdir}/usr/bin/php-fpm
	install -D -m644 ${srcdir}/build-fpm/sapi/fpm/php-fpm.8 ${pkgdir}/usr/share/man/man8/php-fpm.8
	install -D -m644 ${srcdir}/build-fpm/sapi/fpm/php-fpm.conf ${pkgdir}/etc/php/php-fpm.conf
	install -D -m644 ${srcdir}/logrotate.d.php-fpm ${pkgdir}/etc/logrotate.d/php-fpm
	install -d -m755 ${pkgdir}/etc/php/fpm.d
	install -D -m644 ${srcdir}/php-fpm.tmpfiles ${pkgdir}/usr/lib/tmpfiles.d/php-fpm.conf
	install -D -m644 ${srcdir}/php-fpm.service ${pkgdir}/usr/lib/systemd/system/php-fpm.service
}

package_php55-embed() {
	pkgdesc='Embed SAPI for PHP'
	depends=('php55')
	conflicts=('php-embed')
	provides=('php-embed')

	install -D -m755 ${srcdir}/build-embed/libs/libphp5.so ${pkgdir}/usr/lib/libphp5.so
	install -D -m644 ${srcdir}/${pkgbase}-${pkgver}/sapi/embed/php_embed.h ${pkgdir}/usr/include/php/sapi/embed/php_embed.h
}

package_php55-pear() {
	pkgdesc='PHP Extension and Application Repository'
	depends=('php55')
	conflicts=('php-pear')
	provides=('php-pear')

	backup=('etc/php/pear.conf')

	cd ${srcdir}/build-pear
	make install-pear INSTALL_ROOT=${pkgdir}
	rm -rf ${pkgdir}/usr/share/pear/.{channels,depdb,depdblock,filemap,lock,registry}
}

package_php55-enchant() {
	depends=('php55' 'enchant')
	pkgdesc='enchant module for PHP'
	install -D -m755 ${srcdir}/build-php/modules/enchant.so ${pkgdir}/usr/lib/php/modules/enchant.so
}

package_php55-gd() {
	depends=('php55' 'libpng' 'libjpeg' 'freetype2' 'libvpx')
	pkgdesc='gd module for PHP'
	install -D -m755 ${srcdir}/build-php/modules/gd.so ${pkgdir}/usr/lib/php/modules/gd.so
}

package_php55-intl() {
	depends=('php55' 'icu')
	pkgdesc='intl module for PHP'
	install -D -m755 ${srcdir}/build-php/modules/intl.so ${pkgdir}/usr/lib/php/modules/intl.so
}

package_php55-ldap() {
	depends=('php55' 'libldap')
	pkgdesc='ldap module for PHP'
	install -D -m755 ${srcdir}/build-php/modules/ldap.so ${pkgdir}/usr/lib/php/modules/ldap.so
}

package_php55-mcrypt() {
	depends=('php55' 'libmcrypt' 'libltdl')
	pkgdesc='mcrypt module for PHP'
	install -D -m755 ${srcdir}/build-php/modules/mcrypt.so ${pkgdir}/usr/lib/php/modules/mcrypt.so
}

package_php55-mssql() {
	depends=('php55' 'freetds')
	pkgdesc='mssql module for PHP'
	install -D -m755 ${srcdir}/build-php/modules/mssql.so ${pkgdir}/usr/lib/php/modules/mssql.so
}

package_php55-odbc() {
	depends=('php55' 'unixodbc')
	pkgdesc='ODBC modules for PHP'
	install -D -m755 ${srcdir}/build-php/modules/odbc.so ${pkgdir}/usr/lib/php/modules/odbc.so
	install -D -m755 ${srcdir}/build-php/modules/pdo_odbc.so ${pkgdir}/usr/lib/php/modules/pdo_odbc.so
}

package_php55-pgsql() {
	depends=('php55' 'postgresql-libs')
	pkgdesc='PostgreSQL modules for PHP'
	install -D -m755 ${srcdir}/build-php/modules/pgsql.so ${pkgdir}/usr/lib/php/modules/pgsql.so
	install -D -m755 ${srcdir}/build-php/modules/pdo_pgsql.so ${pkgdir}/usr/lib/php/modules/pdo_pgsql.so
}

package_php55-pspell() {
	depends=('php55' 'aspell')
	pkgdesc='pspell module for PHP'
	install -D -m755 ${srcdir}/build-php/modules/pspell.so ${pkgdir}/usr/lib/php/modules/pspell.so
}

package_php55-snmp() {
	depends=('php55' 'net-snmp')
	pkgdesc='snmp module for PHP'
	install -D -m755 ${srcdir}/build-php/modules/snmp.so ${pkgdir}/usr/lib/php/modules/snmp.so
}

package_php55-sqlite() {
	depends=('php55' 'sqlite')
	pkgdesc='sqlite module for PHP'
	install -D -m755 ${srcdir}/build-php/modules/sqlite3.so ${pkgdir}/usr/lib/php/modules/sqlite3.so
	install -D -m755 ${srcdir}/build-php/modules/pdo_sqlite.so ${pkgdir}/usr/lib/php/modules/pdo_sqlite.so
}

package_php55-tidy() {
	depends=('php55' 'tidyhtml')
	pkgdesc='tidy module for PHP'
	install -D -m755 ${srcdir}/build-php/modules/tidy.so ${pkgdir}/usr/lib/php/modules/tidy.so
}

package_php55-xsl() {
	depends=('php55' 'libxslt')
	pkgdesc='xsl module for PHP'
	install -D -m755 ${srcdir}/build-php/modules/xsl.so ${pkgdir}/usr/lib/php/modules/xsl.so
}
