#sqlite3StrAccumInit()函数
<pre>
void sqlite3StrAccumInit(StrAccum *p, char *zBase, int n, int mx){
  p->zText = p->zBase = zBase;
  p->db = 0;
  p->nChar = 0;
  p->nAlloc = n;
  p->mxAlloc = mx;
  p->useMalloc = 1;
  p->tooBig = 0;
  p->mallocFailed = 0;
}
功能：这个函数用于初始化一个字符累加器。
</pre>
