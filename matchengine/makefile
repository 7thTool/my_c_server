TARGET  := matchengine.exe
INCS = -I ../network -I ../utils
LIBS = -L ../utils -lutils -L ../network -lnetwork -Wl,-Bstatic -lev -ljansson -lmpdec -lrdkafka -lhiredis -Wl,-Bdynamic -lm -lmysqlclient -lz -lssl -lcrypto -lpthread -ldl
include ../makefile.inc
