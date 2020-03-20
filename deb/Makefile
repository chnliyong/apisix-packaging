JOBS=1

APISIX_VERSION=1.1
APISIX_REVISION=0

DISTRO:=$(shell lsb_release -c | awk '{print $$2}')

.PHONY: apisix-download
apisix-download:
	sudo apt-get -y -qq install luarocks
	rm -f *.orig.tar.*
	rm -rf /tmp/apisix-$(APISIX_VERSION) && mkdir -p /tmp/apisix-$(APISIX_VERSION)
	sudo luarocks install apisix $(APISIX_VERSION)-$(APISIX_REVISION) --tree=/tmp/apisix-$(APISIX_VERSION)/deps
	# As luarocks install conf logs under /usr/local/apisix, copy them.
	sudo cp -r /usr/local/apisix/* /tmp/apisix-$(APISIX_VERSION) && \
		sudo chown -R $(USER):$(USER) /tmp/apisix-$(APISIX_VERSION)
	# Move the apisix lua source
	cd /tmp/apisix-$(APISIX_VERSION) && mkdir bin && \
		mv deps/share/lua/5.1/apisix/lua ./ && \
		mv deps/lib/luarocks/rocks/apisix/$(APISIX_VERSION)-$(APISIX_REVISION)/bin/apisix bin/ && \
		rm -rf deps/lib/luarocks/
	# Generate the source tar.gz
	tar -czf apisix_$(APISIX_VERSION).orig.tar.gz -C /tmp apisix-$(APISIX_VERSION)

.PHONY: apisix-build
apisix-build: | apisix-clean apisix-download
	sudo apt-get -y -qq install libtemplate-perl
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
	rm -f apisix*.deb
	rm -f apisix_*.*
	sudo rm -rf /usr/local/apisix
