TARGET  := alertcenter.exe
INCS = -I ../network -I ../utils
LIBS = -L ../utils -lutils -L ../network -lnetwork -Wl,-Bstatic -lev -lhiredis -ljansson -lmpdec -Wl,-Bdynamic -lm -lcurl -lssl -lcrypto
include ../makefile.inc
