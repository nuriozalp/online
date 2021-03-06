# Version number of loleaflet, no need to be in sync with the loolwsd
# one, but do please follow the same even/odd convention for the third
# ("micro") part: Between releases odd, even for releases (no other
# changes inbetween).

VERSION=2.0.0

# Version number of the bundled 'draw' thing
DRAW_VERSION=0.2.4

all:
	npm install
	jake build
	jake lint
	rm -rf dist/plugins/draw-$(DRAW_VERSION) && mkdir -p dist/plugins/draw-$(DRAW_VERSION)
	cd plugins/draw-$(DRAW_VERSION) && jake build && cp -ar dist ../../dist/plugins/draw-$(DRAW_VERSION)

.PHONY: dist
dist: all
	rm -rf loleaflet-$(VERSION)
	mkdir loleaflet-$(VERSION)
	cp -ar dist loleaflet-$(VERSION)
	util/po2json.py --quiet po/*.po
	mv po/*.json loleaflet-$(VERSION)/dist/l10n
	util/po2json.py --quiet po/styles/*.po
	mkdir -p loleaflet-$(VERSION)/dist/l10n/styles/
	mv po/styles/*.json loleaflet-$(VERSION)/dist/l10n/styles/
	tar cfz loleaflet-$(VERSION).tar.gz loleaflet-$(VERSION)
	rm -rf loleaflet-$(VERSION)

pot:
	xgettext --from-code=UTF-8 --keyword=_ --output=po/loleaflet-ui.pot \
		evol.colorpicker.strings.js \
		unocommands.js \
		dist/errormessages.js \
		dist/toolbar/toolbar.js \
		src/admin/AdminSocketBase.js \
		src/admin/AdminSocketOverview.js \
		src/admin/AdminStrings.js \
		src/admin/Util.js \
		src/control/Control.Menubar.js \
		src/control/Control.Tabs.js \
		src/core/Socket.js \
		src/map/Map.js

	html2po --pot --input=dist/loleaflet-help.html --output=po/loleaflet-help.pot

l10n: pot
	for i in po/ui-*.po; do pot2po --input=po/loleaflet-ui.pot --template=$$i --output=$$i.new; mv $$i.new $$i;done
	for i in po/help-*.po; do pot2po --input=po/loleaflet-help.pot --template=$$i --output=$$i.new; mv $$i.new $$i;done
	rm po/loleaflet-ui.pot
	rm po/loleaflet-help.pot

spec/data/load-test:
	if [ ! -d spec/data/load_test ]; then \
		mkdir -p spec/data/load_test; \
		for i in $$(seq 1 125); \
		do \
			cp spec/data/eval.odt spec/data/load_test/eval$$i.odt; \
			cp spec/data/eval.odp spec/data/load_test/eval$$i.odp; \
			cp spec/data/eval.ods spec/data/load_test/eval$$i.ods; \
			cp spec/data/eval.odg spec/data/load_test/eval$$i.odg; \
		done; \
	fi

load-test: spec/data/load-test
	rm -rf load_test_out; \
	mkdir load_test_out; \
	for i in $$(seq 1 20); \
	do \
		node_modules/.bin/mocha spec/headlessLoadTest.js > load_test_out/$$i.out 2>&1 & \
	done;
