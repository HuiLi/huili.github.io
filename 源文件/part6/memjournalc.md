#  memjournal.c

主要由叶延婷分析，暂时只做简要介绍。此文件memjournal.c用于实现内存回滚日志，journal_mode=MEMORY需开启。文件开头定义回滚的空间大小，最大1M

        #define JOURNAL_CHUNKSIZE ((int)(1024-sizeof(FileChunk*)))

