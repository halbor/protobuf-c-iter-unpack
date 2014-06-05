AM_CPPFLAGS=-O2 -g3 -Wall

noinst_PROGRAMS = test benchmark benchmark_cpp

test_SOURCES = test.pb-c.c ssrtb_v6.pb-c.c test.pb-iter.c ssrtb_v6.pb-iter.c test.c
test_LDFLAGS = -lprotobuf-c

benchmark_SOURCES = test.pb-c.c ssrtb_v6.pb-c.c test.pb-iter.c ssrtb_v6.pb-iter.c benchmark.c
benchmark_LDFLAGS = -lprotobuf-c

benchmark_cpp_SOURCES = test.pb.cc ssrtb_v6.pb.cc benchmark_cpp.cc
benchmark_cpp_LDFLAGS = -lprotobuf -lpthread

CLEANFILES = *.pb-c.[ch] *.pb-iter.[ch] *.pyc *.pb.cc *.pb.h

%.pb-c.c %.pb-c.h: %.proto
	protoc-c --c_out=. $<

%.pb-iter.c %.pb-iter.h: %.proto
	protoc --plugin=protoc-gen-c-iter=../../protoc-gen-c-iter --c-iter_out=. $<

%.pb.cc %.pb.h: %.proto
	protoc --cpp_out=. $<

