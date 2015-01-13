# fts3_expr.c简介doc
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;而fts3_expr.c这个文件主要是实现查询字符串功能（MATCH函数）。MATCH运算符用在全文检索中。例如这两句:

    SELECT title, body FROM pages WHERE pages MATCH 'world';
    SELECT title, body FROM pages WHERE title MATCH 'world';

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;这两句，前一个 MATCH 左边写了表名，后一个写的是列名。后一个仅搜索title列，前一个是搜索全部列（docid 列以外）。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;MATCH右侧的表达式支持模糊查询、支持指定列查询、支持 AND/OR/NEAR/NOT 等运算，例如：

    SELECT title, body FROM pages WHERE pages MATCH 'hel*';
    SELECT title, body FROM pages WHERE pages MATCH 'title:hello';
    SELECT title, body FROM pages WHERE pages MATCH 'hello AND world';
    SELECT title, body FROM pages WHERE pages MATCH '(hello NEAR world) OR (program AND language)';

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;但要注意只能出现一次 MATCH 判断，

    WHERE title MATCH 'hello' AND body MATCH 'world';

是不行的，可以改作

    WHERE pages MATCH 'title:hello AND body:world';

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;本模块主要定义了以下几个函数：

<table class=MsoNormalTable border=1 cellspacing=0 cellpadding=0
 style='border-collapse:collapse;border:none;mso-border-alt:solid windowtext .5pt;
 mso-yfti-tbllook:1184;mso-padding-alt:0cm 5.4pt 0cm 5.4pt;mso-border-insideh:
 .5pt solid windowtext;mso-border-insidev:.5pt solid windowtext'>
 <tr style='mso-yfti-irow:0;mso-yfti-firstrow:yes'>
  <td width=284 valign=top style='width:213.05pt;border:solid windowtext 1.0pt;
  mso-border-alt:solid windowtext .5pt;padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal style='text-indent:24.0pt'><span style='font-family:"微软雅黑","sans-serif";
  mso-ascii-font-family:宋体;mso-hansi-font-family:宋体'>函数名</span><span
  lang=EN-US style='font-family:宋体;mso-fareast-font-family:微软雅黑'><o:p></o:p></span></p>
  </td>
  <td width=284 valign=top style='width:213.05pt;border:solid windowtext 1.0pt;
  border-left:none;mso-border-left-alt:solid windowtext .5pt;mso-border-alt:
  solid windowtext .5pt;padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal style='text-indent:24.0pt'><span style='mso-bidi-font-size:
  14.0pt;font-family:"微软雅黑","sans-serif";mso-ascii-font-family:Arial;
  mso-hansi-font-family:Arial;mso-bidi-font-family:Arial;color:#333333;
  background:white'>函数功能</span><span lang=EN-US style='mso-bidi-font-size:14.0pt;
  font-family:"Arial","sans-serif";color:#333333;background:white'><o:p></o:p></span></p>
  </td>
 </tr>
 <tr style='mso-yfti-irow:1'>
  <td width=284 valign=top style='width:213.05pt;border:solid windowtext 1.0pt;
  border-top:none;mso-border-top-alt:solid windowtext .5pt;mso-border-alt:solid windowtext .5pt;
  padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal style='text-indent:24.0pt'><span lang=EN-US
  style='font-family:宋体;mso-fareast-font-family:微软雅黑'>fts3isspace(char c)<o:p></o:p></span></p>
  </td>
  <td width=284 valign=top style='width:213.05pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  mso-border-top-alt:solid windowtext .5pt;mso-border-left-alt:solid windowtext .5pt;
  mso-border-alt:solid windowtext .5pt;padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal style='text-indent:24.0pt'><span style='mso-bidi-font-size:
  14.0pt;font-family:"微软雅黑","sans-serif";mso-ascii-font-family:Arial;
  mso-hansi-font-family:Arial;mso-bidi-font-family:Arial;color:#333333;
  background:white'>检查参数</span><span lang=EN-US style='mso-bidi-font-size:14.0pt;
  font-family:"Arial","sans-serif";color:#333333;background:white'>c</span><span
  style='mso-bidi-font-size:14.0pt;font-family:"微软雅黑","sans-serif";mso-ascii-font-family:
  Arial;mso-hansi-font-family:Arial;mso-bidi-font-family:Arial;color:#333333;
  background:white'>是否为空格字符</span><span lang=EN-US style='mso-bidi-font-size:
  14.0pt;font-family:宋体;mso-fareast-font-family:微软雅黑'><o:p></o:p></span></p>
  </td>
 </tr>
 <tr style='mso-yfti-irow:2'>
  <td width=284 valign=top style='width:213.05pt;border:solid windowtext 1.0pt;
  border-top:none;mso-border-top-alt:solid windowtext .5pt;mso-border-alt:solid windowtext .5pt;
  padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal style='text-indent:24.0pt'><span lang=EN-US
  style='font-family:宋体;mso-fareast-font-family:微软雅黑'>*fts3MallocZero(<span
  class=SpellE>int</span> <span class=SpellE>nByte</span>)<o:p></o:p></span></p>
  </td>
  <td width=284 valign=top style='width:213.05pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  mso-border-top-alt:solid windowtext .5pt;mso-border-left-alt:solid windowtext .5pt;
  mso-border-alt:solid windowtext .5pt;padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal style='text-indent:24.0pt'><span style='font-family:"微软雅黑","sans-serif";
  mso-ascii-font-family:宋体;mso-hansi-font-family:宋体'>分配内存字节</span><span
  lang=EN-US style='font-family:宋体;mso-fareast-font-family:微软雅黑'><o:p></o:p></span></p>
  </td>
 </tr>
 <tr style='mso-yfti-irow:3'>
  <td width=284 valign=top style='width:213.05pt;border:solid windowtext 1.0pt;
  border-top:none;mso-border-top-alt:solid windowtext .5pt;mso-border-alt:solid windowtext .5pt;
  padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal style='text-indent:24.0pt'><span lang=EN-US
  style='font-family:宋体;mso-fareast-font-family:微软雅黑'>sqlite3Fts3OpenTokenizer<o:p></o:p></span></p>
  <p class=MsoNormal style='text-indent:24.0pt'><span lang=EN-US
  style='font-family:宋体;mso-fareast-font-family:微软雅黑'><o:p>&nbsp;</o:p></span></p>
  </td>
  <td width=284 valign=top style='width:213.05pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  mso-border-top-alt:solid windowtext .5pt;mso-border-left-alt:solid windowtext .5pt;
  mso-border-alt:solid windowtext .5pt;padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal style='text-indent:24.0pt'><span style='font-family:"微软雅黑","sans-serif";
  mso-ascii-font-family:宋体;mso-hansi-font-family:宋体'>打开分词器</span><span
  lang=EN-US style='font-family:宋体;mso-fareast-font-family:微软雅黑'><o:p></o:p></span></p>
  </td>
 </tr>
 <tr style='mso-yfti-irow:4'>
  <td width=284 valign=top style='width:213.05pt;border:solid windowtext 1.0pt;
  border-top:none;mso-border-top-alt:solid windowtext .5pt;mso-border-alt:solid windowtext .5pt;
  padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal style='text-indent:24.0pt'><span class=SpellE><span
  lang=EN-US style='font-family:宋体;mso-fareast-font-family:微软雅黑'>getNextToken</span></span><span
  lang=EN-US style='font-family:宋体;mso-fareast-font-family:微软雅黑'><o:p></o:p></span></p>
  </td>
  <td width=284 valign=top style='width:213.05pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  mso-border-top-alt:solid windowtext .5pt;mso-border-left-alt:solid windowtext .5pt;
  mso-border-alt:solid windowtext .5pt;padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal style='text-indent:24.0pt'><span style='font-family:"微软雅黑","sans-serif";
  mso-ascii-font-family:宋体;mso-hansi-font-family:宋体'>提取下一个标记和其他语法分析的信息</span><span
  lang=EN-US style='font-family:宋体;mso-fareast-font-family:微软雅黑'><o:p></o:p></span></p>
  </td>
 </tr>
 <tr style='mso-yfti-irow:5'>
  <td width=284 valign=top style='width:213.05pt;border:solid windowtext 1.0pt;
  border-top:none;mso-border-top-alt:solid windowtext .5pt;mso-border-alt:solid windowtext .5pt;
  padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal style='text-indent:24.0pt'><span lang=EN-US
  style='font-family:宋体;mso-fareast-font-family:微软雅黑'>*fts3ReallocOrFree<o:p></o:p></span></p>
  </td>
  <td width=284 valign=top style='width:213.05pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  mso-border-top-alt:solid windowtext .5pt;mso-border-left-alt:solid windowtext .5pt;
  mso-border-alt:solid windowtext .5pt;padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal style='text-indent:24.0pt'><span style='font-family:"微软雅黑","sans-serif";
  mso-ascii-font-family:宋体;mso-hansi-font-family:宋体'>扩大内存分配</span><span
  lang=EN-US style='font-family:宋体;mso-fareast-font-family:微软雅黑'><o:p></o:p></span></p>
  </td>
 </tr>
 <tr style='mso-yfti-irow:6'>
  <td width=284 valign=top style='width:213.05pt;border:solid windowtext 1.0pt;
  border-top:none;mso-border-top-alt:solid windowtext .5pt;mso-border-alt:solid windowtext .5pt;
  padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal style='text-indent:24.0pt'><span class=SpellE><span
  lang=EN-US style='font-family:宋体;mso-fareast-font-family:微软雅黑'>getNextString</span></span><span
  lang=EN-US style='font-family:宋体;mso-fareast-font-family:微软雅黑'><o:p></o:p></span></p>
  </td>
  <td width=284 valign=top style='width:213.05pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  mso-border-top-alt:solid windowtext .5pt;mso-border-left-alt:solid windowtext .5pt;
  mso-border-alt:solid windowtext .5pt;padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal style='text-indent:24.0pt'><span style='font-family:"微软雅黑","sans-serif";
  mso-ascii-font-family:宋体;mso-hansi-font-family:宋体'>处理整个缓冲输入和创建包含结果的类型为</span><span
  lang=EN-US style='font-family:宋体;mso-fareast-font-family:微软雅黑'>FTSQUERY_PHRASE</span><span
  style='font-family:"微软雅黑","sans-serif";mso-ascii-font-family:宋体;mso-hansi-font-family:
  宋体'>的</span><span lang=EN-US style='font-family:宋体;mso-fareast-font-family:
  微软雅黑'>Fts3Expr</span><span style='font-family:"微软雅黑","sans-serif";mso-ascii-font-family:
  宋体;mso-hansi-font-family:宋体'>结构体</span><span lang=EN-US style='font-family:
  宋体;mso-fareast-font-family:微软雅黑'><o:p></o:p></span></p>
  </td>
 </tr>
 <tr style='mso-yfti-irow:7'>
  <td width=284 valign=top style='width:213.05pt;border:solid windowtext 1.0pt;
  border-top:none;mso-border-top-alt:solid windowtext .5pt;mso-border-alt:solid windowtext .5pt;
  padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal style='text-indent:24.0pt'><span lang=EN-US
  style='font-family:宋体;mso-fareast-font-family:微软雅黑'>fts3ExprParse<o:p></o:p></span></p>
  </td>
  <td width=284 valign=top style='width:213.05pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  mso-border-top-alt:solid windowtext .5pt;mso-border-left-alt:solid windowtext .5pt;
  mso-border-alt:solid windowtext .5pt;padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal style='text-indent:24.0pt'><span style='font-family:"微软雅黑","sans-serif";
  mso-ascii-font-family:宋体;mso-hansi-font-family:宋体'>被函数</span><span
  lang=EN-US style='font-family:宋体;mso-fareast-font-family:微软雅黑'>fts3ExprParse()</span><span
  style='font-family:"微软雅黑","sans-serif";mso-ascii-font-family:宋体;mso-hansi-font-family:
  宋体'>所引用</span><span lang=EN-US style='font-family:宋体;mso-fareast-font-family:
  微软雅黑'><o:p></o:p></span></p>
  </td>
 </tr>
 <tr style='mso-yfti-irow:8'>
  <td width=284 valign=top style='width:213.05pt;border:solid windowtext 1.0pt;
  border-top:none;mso-border-top-alt:solid windowtext .5pt;mso-border-alt:solid windowtext .5pt;
  padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal style='text-indent:24.0pt'><span class=SpellE><span
  lang=EN-US style='font-family:宋体;mso-fareast-font-family:微软雅黑'>getNextNode</span></span><span
  lang=EN-US style='font-family:宋体;mso-fareast-font-family:微软雅黑'><o:p></o:p></span></p>
  </td>
  <td width=284 valign=top style='width:213.05pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  mso-border-top-alt:solid windowtext .5pt;mso-border-left-alt:solid windowtext .5pt;
  mso-border-alt:solid windowtext .5pt;padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal style='text-indent:24.0pt'><span style='font-family:"微软雅黑","sans-serif";
  mso-ascii-font-family:宋体;mso-hansi-font-family:宋体'>查找关键词</span><span
  lang=EN-US style='font-family:宋体;mso-fareast-font-family:微软雅黑'><o:p></o:p></span></p>
  </td>
 </tr>
 <tr style='mso-yfti-irow:9'>
  <td width=284 valign=top style='width:213.05pt;border:solid windowtext 1.0pt;
  border-top:none;mso-border-top-alt:solid windowtext .5pt;mso-border-alt:solid windowtext .5pt;
  padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal style='text-indent:24.0pt'><span class=SpellE><span
  lang=EN-US style='font-family:宋体;mso-fareast-font-family:微软雅黑'>opPrecedence</span></span><span
  lang=EN-US style='font-family:宋体;mso-fareast-font-family:微软雅黑'><o:p></o:p></span></p>
  </td>
  <td width=284 valign=top style='width:213.05pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  mso-border-top-alt:solid windowtext .5pt;mso-border-left-alt:solid windowtext .5pt;
  mso-border-alt:solid windowtext .5pt;padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal style='text-indent:24.0pt'><span style='font-family:"微软雅黑","sans-serif";
  mso-ascii-font-family:宋体;mso-hansi-font-family:宋体'>返回一个代表操作优先级的整数</span><span
  lang=EN-US style='font-family:宋体;mso-fareast-font-family:微软雅黑'><o:p></o:p></span></p>
  </td>
 </tr>
 <tr style='mso-yfti-irow:10'>
  <td width=284 valign=top style='width:213.05pt;border:solid windowtext 1.0pt;
  border-top:none;mso-border-top-alt:solid windowtext .5pt;mso-border-alt:solid windowtext .5pt;
  padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal style='text-indent:24.0pt'><span class=SpellE><span
  lang=EN-US style='font-family:宋体;mso-fareast-font-family:微软雅黑'>insertBinaryOperator</span></span><span
  lang=EN-US style='font-family:宋体;mso-fareast-font-family:微软雅黑'><o:p></o:p></span></p>
  </td>
  <td width=284 valign=top style='width:213.05pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  mso-border-top-alt:solid windowtext .5pt;mso-border-left-alt:solid windowtext .5pt;
  mso-border-alt:solid windowtext .5pt;padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal style='text-indent:24.0pt'><span style='font-family:"微软雅黑","sans-serif";
  mso-ascii-font-family:宋体;mso-hansi-font-family:宋体'>把二元操作插入查询表达树</span><span
  lang=EN-US style='font-family:宋体;mso-fareast-font-family:微软雅黑'><o:p></o:p></span></p>
  </td>
 </tr>
 <tr style='mso-yfti-irow:11'>
  <td width=284 valign=top style='width:213.05pt;border:solid windowtext 1.0pt;
  border-top:none;mso-border-top-alt:solid windowtext .5pt;mso-border-alt:solid windowtext .5pt;
  padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal style='text-indent:24.0pt'><span lang=EN-US
  style='font-family:宋体;mso-fareast-font-family:微软雅黑'>fts3ExprParse<o:p></o:p></span></p>
  </td>
  <td width=284 valign=top style='width:213.05pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  mso-border-top-alt:solid windowtext .5pt;mso-border-left-alt:solid windowtext .5pt;
  mso-border-alt:solid windowtext .5pt;padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal style='text-indent:24.0pt'><span style='font-family:"微软雅黑","sans-serif";
  mso-ascii-font-family:宋体;mso-hansi-font-family:宋体'>解析</span><span lang=EN-US
  style='font-family:宋体;mso-fareast-font-family:微软雅黑'>fts3</span><span
  style='font-family:"微软雅黑","sans-serif";mso-ascii-font-family:宋体;mso-hansi-font-family:
  宋体'>查询表达式</span><span lang=EN-US style='font-family:宋体;mso-fareast-font-family:
  微软雅黑'><o:p></o:p></span></p>
  </td>
 </tr>
 <tr style='mso-yfti-irow:12;mso-yfti-lastrow:yes'>
  <td width=284 valign=top style='width:213.05pt;border:solid windowtext 1.0pt;
  border-top:none;mso-border-top-alt:solid windowtext .5pt;mso-border-alt:solid windowtext .5pt;
  padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal style='text-indent:24.0pt'><span lang=EN-US
  style='font-family:宋体;mso-fareast-font-family:微软雅黑'>sqlite3Fts3ExprParse<o:p></o:p></span></p>
  </td>
  <td width=284 valign=top style='width:213.05pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  mso-border-top-alt:solid windowtext .5pt;mso-border-left-alt:solid windowtext .5pt;
  mso-border-alt:solid windowtext .5pt;padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal style='text-indent:24.0pt'><span style='font-family:"微软雅黑","sans-serif";
  mso-ascii-font-family:宋体;mso-hansi-font-family:宋体'>解析一个查询表达式和创建一个表示已被查询的表达式的</span><span
  lang=EN-US style='font-family:宋体;mso-fareast-font-family:微软雅黑'>Fts3Expr</span><span
  style='font-family:"微软雅黑","sans-serif";mso-ascii-font-family:宋体;mso-hansi-font-family:
  宋体'>结构体树</span><span lang=EN-US style='font-family:宋体;mso-fareast-font-family:
  微软雅黑'><o:p></o:p></span></p>
  </td>
 </tr>
</table>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;总的来说，这个模块主要是用于处理和分析表达式，说明这个模块之前必须先声明，当SQLITE_ENABLE_FTS3_PARENTHESIS参数被定义时，此模块将使用新的语法：

    	新的句法支持括号，旧的不支持。
    	新的句法支持AND和NOT操作，旧的不支持。
    	旧的句法支持"-"符号是否是合格的验证，而新的句法不支持（以NOT操作代替）。
    	当使用旧的句法时，OR操作比隐含的AND有更大的优先级，用新句法时，不论是隐式 还是显式的，AND都比OR有更高的优先级。


