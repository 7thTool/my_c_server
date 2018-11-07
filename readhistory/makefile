TARGET  := readhistory.exe
INCS = -I ../network -I ../utils
LIBS = -L ../utils -lutils -L ../network -lnetwork -Wl,-Bstatic -lev -ljansson -lmpdec -lhiredis -Wl,-Bdynamic -lm -lmysqlclient -lz -lpthread -ldl
include ../makefile.inc
