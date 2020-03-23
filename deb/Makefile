JOBS=1

APISIX_VERSION=1.1
APISIX_REVISION=0

DISTRO:=$(shell lsb_release -c | awk '{print $$2}')

.PHONY: apisix-download
apisix-download:
	rm -f *.orig.tar.*
	curl -L -o apisix_$(APISIX_VERSION).orig.tar.gz https://github.com/apache/incubator-apisix/archive/$(APISIX_VERSION).tar.gz

.PHONY: apisix-build
apisix-build: | apisix-clean apisix-download
	sudo apt-get -y -qq install luarocks libtemplate-perl
	rm -f *.deb *.debian.tar.xz *.dsc *.changes
	tar xf apisix_$(APISIX_VERSION).orig.tar.gz --strip-components=1 -C apisix
	cd apisix && \
		tpage --define distro=$(DISTRO) debian/changelog.tt2 > debian/changelog && \
		debuild $(OPTS) -j$(JOBS)

.PHONY: apisix-clean
apisix-clean:
	cd apisix && debclean
	# remove all files under apisix other than `apisix`, `apisix/debian`
	find apisix -maxdepth 1 ! -name 'debian' ! -name 'apisix' -print | xargs rm -rf
	rm -f apisix*.deb apisix*.ddeb
	rm -f apisix_*.*
