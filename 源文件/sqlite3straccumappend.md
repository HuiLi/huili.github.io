# sqlite3StrAccumAppend()函数
<pre>
void sqlite3StrAccumAppend(StrAccum *p, const char *z, int N){
  assert( z!=0 || N==0 );
  if( p->tooBig | p->mallocFailed ){
    testcase(p->tooBig);
    testcase(p->mallocFailed);
    return;
  }
  assert( p->zText!=0 || p->nChar==0 );
  if( N<0 ){
    N = sqlite3Strlen30(z);
  }
  if( N==0 || NEVER(z==0) ){
    return;
  }
  if( p->nChar+N >= p->nAlloc ){
    char *zNew;
    if( !p->useMalloc ){
      p->tooBig = 1;
      N = p->nAlloc - p->nChar - 1;
      if( N<=0 ){
        return;
      }
    }else{
      char *zOld = (p->zText==p->zBase ? 0 : p->zText);
      i64 szNew = p->nChar;
      szNew += N + 1;
      if( szNew > p->mxAlloc ){
        sqlite3StrAccumReset(p);
        p->tooBig = 1;
        return;
      }else{
        p->nAlloc = (int)szNew;
      }
      if( p->useMalloc==1 ){
        zNew = sqlite3DbRealloc(p->db, zOld, p->nAlloc);
      }else{
        zNew = sqlite3_realloc(zOld, p->nAlloc);
      }
      if( zNew ){
        if( zOld==0 && p->nChar>0 ) memcpy(zNew, p->zText, p->nChar);
        p->zText = zNew;
      }else{
        p->mallocFailed = 1;
        sqlite3StrAccumReset(p);
        return;
      }
    }
  }
  assert( p->zText );
  memcpy(&p->zText[p->nChar], z, N);
  p->nChar += N;
}
功能：从z到StrAccumulated对象，都增加n个文本字节
函数引用：<img src="p5.png">
</pre>
