include_fpllldir=$(includedir)/fplll

include_fplll_HEADERS=defs.h dpe.h fplll.h matrix.h matrix.cpp nr.h \
	nr.cpp numvect.h util.h svpcvp.h fplllv31.h bkz.h lll.h gso.h evaluator.h

EXTRA_DIST=example_in example_out example_svp_in example_svp_out dim55_in \
	pruning.txt

bin_PROGRAMS=fplll latticegen
check_PROGRAMS=llldiff
EXTRA_PROGRAMS=fplll_dbg latticegen_dbg test 
lib_LTLIBRARIES=libfplll.la
EXTRA_LTLIBRARIES=libfplllv.la libfpllld.la

fplll_SOURCES=main.cpp main.h
fplll_LDADD=libfplll.la
fplll_dbg_SOURCES=$(fplll_SOURCES)
fplll_dbg_CPPFLAGS=-DDEBUG $(AM_CPPFLAGS)
fplll_dbg_CXXFLAGS=-g $(AM_CXXFLAGS)
fplll_dbg_LDADD=libfpllld.la

latticegen_SOURCES=latticegen.cpp
latticegen_LDADD=libfplll.la
latticegen_dbg_SOURCES=$(latticegen_SOURCES)
latticegen_dbg_CPPFLAGS=-DDEBUG $(AM_CPPFLAGS)
latticegen_dbg_CXXFLAGS=-g $(AM_CXXFLAGS)
latticegen_dbg_LDADD=libfpllld.la

llldiff_SOURCES=llldiff.cpp
llldiff_LDADD=libfplll.la

test_SOURCES=test.cpp
test_LDADD=libfplll.la

libfplll_la_SOURCES=fplll.cpp fplll.h util.cpp util.h topenum.cpp topenum.h \
	evaluator.cpp evaluator.h
EXTRA_libfplll_la_SOURCES=gso.cpp gso.h lll.cpp lll.h wrapper.cpp wrapper.h \
	bkz.cpp bkz.h enumerate.cpp enumerate.h svpcvp.cpp
libfplll_la_LIBADD=-lgmp -lmpfr
libfplll_la_LDFLAGS=-no-undefined -version-info 1:0:1

libfplllv_la_SOURCES=$(libfplll_la_SOURCES)
EXTRA_libfplllv_la_SOURCES=$(EXTRA_libfplll_la_SOURCES)
libfplllv_la_CPPFLAGS=$(AM_CPPFLAGS)
libfplllv_la_LIBADD=$(libfplll_la_LIBADD)

libfpllld_la_SOURCES=$(libfplll_la_SOURCES)
EXTRA_libfpllld_la_SOURCES=$(EXTRA_libfplll_la_SOURCES)
libfpllld_la_CPPFLAGS=-DDEBUG $(AM_CPPFLAGS)
libfpllld_la_CXXFLAGS=-g $(AM_CXXFLAGS)
libfpllld_la_LIBADD=$(libfplll_la_LIBADD)

install-data-hook:
	echo "#include <fplll/fplll.h>" > "$(srcdir)/fplll.h.root"
	echo "using namespace fplll;" >> "$(srcdir)/fplll.h.root"
	$(INSTALL) -m 644 "$(srcdir)/fplll.h.root" "${DESTDIR}$(includedir)/fplll.h"
	rm -f "$(srcdir)/fplll.h.root"

uninstall-hook:
	(test -d "${DESTDIR}$(includedir)/fplll" && rmdir "${DESTDIR}$(includedir)/fplll") || true
	rm -f "${DESTDIR}$(includedir)/fplll.h"

dbg: fplll_dbg latticegen_dbg

check:
	./fplll < $(srcdir)/dim55_in > foo
	./fplll -m proved < foo > foo2
	diff foo foo2
	rm foo foo2
	./latticegen r 50 1000 | ./fplll -m fast -f double > foo
	./fplll -m proved < foo > foo2
	diff foo foo2
	rm foo foo2
	./latticegen r 30 2000 | ./fplll -m heuristic -f dpe > foo
	./fplll -m proved < foo > foo2
	diff foo foo2
	rm foo foo2
	./latticegen r 30 2000 | ./fplll -m proved -f dpe > foo
	./fplll -m proved < foo > foo2
	diff foo foo2
	rm foo foo2	
	./fplll -m heuristic < $(srcdir)/example_in > foo
	cat foo $(srcdir)/example_out | ./llldiff
	rm foo
	./fplll -m fast -f double < $(srcdir)/example_in > foo
	cat foo $(srcdir)/example_out | ./llldiff
	rm foo
	./fplll -m proved < $(srcdir)/example_in > foo
	cat foo $(srcdir)/example_out | ./llldiff
	rm foo
	./fplll -m fastearly -f double < $(srcdir)/example_in > foo
	cat foo $(srcdir)/example_out | ./llldiff
	rm foo
	./fplll -m heuristicearly < $(srcdir)/example_in > foo
	cat foo $(srcdir)/example_out | ./llldiff
	rm foo
	./fplll -a svp < $(srcdir)/example_svp_in > foo
	diff foo $(srcdir)/example_svp_out
