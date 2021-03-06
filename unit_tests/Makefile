# SYNOPSIS:
#
#   make [all]  - makes everything.
#   make TARGET - makes the given target.
#   make clean  - removes all files generated by make.

# Points to the root of Google Test, relative to where this file is.
# Remember to tweak this if you move this file.
GTEST_DIR = gtest-1.4.0

# Where to find user code.
UPDIR = $(realpath ..)
USER_DIR = $(UPDIR)

VPATH = $(USER_DIR)

CC = g++
# Flags passed to the preprocessor.
CPPFLAGS += -I. -I$(GTEST_DIR) -I$(GTEST_DIR)/include $(patsubst %,-I%,$(USER_DIR)) -D__STDC_FORMAT_MACROS -D__STDC_CONSTANT_MACROS -DHAVE_SSE

# Flags passed to the C++ compiler.
# For profiling -fprofile-arcs -ftest-coverage -O0
# TODO: remove lax-vector-conversions
CXXFLAGS += -g -Wall -Wextra -Werror -DSSE -march=native -msse -msse2 -O3

LDFLAGS = -lpthread -lrt -lm

# All tests produced by this Makefile.  Remember to add new tests you
# created to the list.
#TESTS = test_large_block test_mutex test_cond test_packed_key_value_array test_square_binary_matrix test_misc test_offsets_key_value
TESTS = test_offsets_key_value
SOURCES = $(patsubst %,%.cc,$(TESTS))

# All Google Test headers.  Usually you shouldn't change this
# definition.
GTEST_HEADERS = $(GTEST_DIR)/include/gtest/*.h \
                $(GTEST_DIR)/include/gtest/internal/*.h

# House-keeping build targets.

%.d: %.cc
	$(CC) -M -MM -MG -MP $(CPPFLAGS) $< > $@.tmp
	sed -e 's/$*.o/& $@/g' $@.tmp > $@ && rm $@.tmp

all : $(TESTS)

run: all
	@for i in $(TESTS); do ./$$i; done

clean :
	rm -f $(TESTS) gtest.a gtest_main.a *.o

$(TESTS): gtest_main.a

# Some extra dependencies
test_packed_key_value_array: storage.o

test_square_binary_matrix: square_binary_matrix.o

# Builds gtest.a and gtest_main.a.

# Usually you shouldn't tweak such internal variables, indicated by a
# trailing _.
GTEST_SRCS_ = $(GTEST_DIR)/src/*.cc $(GTEST_DIR)/src/*.h $(GTEST_HEADERS)

# For simplicity and to avoid depending on Google Test's
# implementation details, the dependencies specified below are
# conservative and not optimized.  This is fine as Google Test
# compiles fast and for ordinary users its source rarely changes.
gtest-all.o : $(GTEST_SRCS_)
	$(CXX) $(CPPFLAGS) $(CXXFLAGS) -c $(GTEST_DIR)/src/gtest-all.cc

gtest_main.o : $(GTEST_SRCS_)
	$(CXX) $(CPPFLAGS) $(CXXFLAGS) -c $(GTEST_DIR)/src/gtest_main.cc

gtest.a : gtest-all.o
	$(AR) $(ARFLAGS) $@ $^

gtest_main.a : gtest-all.o gtest_main.o
	$(AR) $(ARFLAGS) $@ $^

include $(SOURCES:.cc=.d)
