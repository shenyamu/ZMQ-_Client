
ifeq ($(arch),32)
	ARCH := -m32
	BITS := 32	
else
	ARCH:= -m64
	BITS:=64
	LIBEXT=64
endif

ifeq ($(release),y)
	OUTTYPE := -O2
	OUTCONFIG := release
else
	OUTCONFIG := debug
endif

TOPDIR:=..
OUTDIR=$(TOPDIR)/bin
OBJDIR=$(OUTDIR)/$(BITS)/$(OUTCONFIG)
SRCDIR:=$(TOPDIR)/src
VPATH:=$(SRCDIR):$(SRCDIR)/common:$(SRCDIR)/consumer:$(SRCDIR)/kpr:$(SRCDIR)/message:\
	$(SRCDIR)/producer:$(SRCDIR)/protocol:$(SRCDIR)/transport
DIRS:=$(SRCDIR) $(SRCDIR)/common $(SRCDIR)/consumer $(SRCDIR)/kpr $(SRCDIR)/message \
        $(SRCDIR)/producer $(SRCDIR)/protocol $(SRCDIR)/transport

SOURCE := $(foreach dir,$(DIRS), $(wildcard $(dir)/*.cpp))
OBJS := $(filter-out dllmain.o,$(patsubst %.cpp,%.o, $(notdir $(SOURCE))))

OUT :=librocketmq$(LIBEXT).so
LIBS := jsoncpp$(LIBEXT) pthread z
LIBPATH := -L$(TOPDIR)/libs/jsoncpp/lib -L$(TOPDIR)/libs/curl/lib -L$(TOPDIR)/libs/zlib/lib
INCLUDEPATH :=  -I$(TOPDIR)/include -I$(TOPDIR)/libs/jsoncpp/include -I$(TOPDIR)/libs/curl/include -I$(TOPDIR)/libs/zlib/include $(addprefix -I ,$(DIRS))

CC= g++
CFLAGS := -g -fPIC -Wno-deprecated -mtune=i686 $(ARCH) $(OUTTYPE)
LDFLAGS := -shared -fPIC
CXXFLAGS := $(CFLAGS) $(INCLUDEPATH)

.PHONY : build rebuild clean echoo createdir

all:createdir build
createdir:
	mkdir -p $(OBJDIR)
build:$(OUT)
rebuild:clean build
# lin.qs, 同时清除 project 目录下的 .o 文件
clean:
	$(RM) $(OBJDIR)/$(OBJS) $(OUTDIR)/$(OUT)
	$(RM) $(OBJS)
echoo:
#	echo $(SOURCE)
	@echo $(OBJS)

$(OUT) : $(OBJS)
	$(CC) -o $(OUTDIR)/$(OUT) $(LDFLAGS) $(addprefix $(OBJDIR)/,$(OBJS)) $(LIBPATH) $(addprefix -l ,$(LIBS))

# lin.qs, 在生成 .o 文件时，同时生成一份到当前目录（project）目录下，避免之后每次执行 make all 都是全编
#       之前生成到 OBJDIR 目录下，而当前目录没有，导致每次 make all 都是全编
#       副作用：project 目录下会多一份 .o 文件，占用一定空间；首次编译耗时较长（理论上是原先的2倍）
%.o : %.cpp
	$(CC) -c $(CXXFLAGS) $(CPPFLAGS) $< -o $(OBJDIR)/$@
	$(CC) -c $(CXXFLAGS) $(CPPFLAGS) $< -o $@

