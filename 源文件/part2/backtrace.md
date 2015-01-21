# 关于backtrace
	if( mem.nBacktrace ){ /*如果backtrace深度为0.那么就不用执行*/
	      void *aAddr[40];
	      pHdr->nBacktrace = backtrace(aAddr, mem.nBacktrace+1)-1;
	      memcpy(pBt, &aAddr[1], pHdr->nBacktrace*sizeof(void*));
	      assert(pBt[0]);
	      if( mem.xBacktrace ){/*如果有回调函数，那么执行回调函数*/
	        mem.xBacktrace(nByte, pHdr->nBacktrace-1, &aAddr[1]);
	      }
	    }else{
	      pHdr->nBacktrace = 0;
	    }
可以设置malloc的backtrace的深度和在生成backtrace后可以通过回调函数将其传出去。
