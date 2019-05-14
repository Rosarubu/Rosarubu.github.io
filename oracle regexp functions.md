# oracle regexp functions

来自[在 Oracle 中使用正则表达式](https://www.cnblogs.com/killkill/archive/2010/09/06/1819536.html) 和 [oracle 正则表达式 匹配](https://www.cnblogs.com/qmfsun/p/4467904.html) 稍有改动

1. regexp_like (匹配) 比较一个字符串是否与正则表达式匹配
2. regexp_substr (提取) 返回与正则表达式匹配的子字符串 
3. regexp_instr (包含)在字符串中查找正则表达式，并且返回匹配的位置
4. regexp_replace (替换)搜索并且替换匹配的正则表达式

语法来源：[techonthenet oracle function page](https://www.techonthenet.com/oracle/functions/regexp_substr.php)

举例用表:

```plsql
create table tmp as
with data as (
  select 'like' as id ,'a9999' as str from dual union all
  select 'like'       ,'a9c'          from dual union all
  select 'like'       ,'A7007'        from dual union all
  select 'like'       ,'123a34cc'     from dual union all
  select 'substr'     ,'123,234,345'  from dual union all
  select 'substr'     ,'12,34.56:78'  from dual union all
  select 'substr'     ,'123456789'    from dual union all
  select 'instr'      ,'192.168.0.1'  from dual union all
  select 'replace'    ,'(020)12345678' from dual union all
  select 'replace'    ,'001517729C28' from dual  
)
select * from data ;

select * from tmp ;
ID      STR

------

like    a9999
like    a9c
like    A7007
like    123a34cc
substr  123,234,345
substr  12,34.56:78
substr  123456789
instr   192.168.0.1
replace (020)12345678
replace 001517729C28
```



##### 参数的含义:

1. `match_parameter`，匹配选项

   取值范围： `i`：大小写不敏感； `c`：大小写敏感；`n`：点号 . 不匹配换行符号；`m`：多行模式；`x`：扩展模式，忽略正则表达式中的空白字符

2. `start_position`，标识从第几个字符开始正则表达式匹配

3. `nth_appearance`，标识第几个匹配组

4. `replacement_string`，替换的字符串



##### **`regexp_like`** 

只能用于条件表达式(`where`)，和 `like` 类似，但是使用的正则表达式进行匹配，语法很简单：

`REGEXP_LIKE ( expression, pattern [, match_parameter ] )`

###### `regexp_like`例子

```plsql
select str from tmp where id='like' and regexp_like(str,'A\d+','i'); -- 'i' 忽略大小写
STR
-------------
a9999
a9c
A7007
123a34cc
 
select str from tmp where id='like' and regexp_like(str, 'a\d+');
STR
-------------
a9999
a9c
123a34cc
 
select str from tmp where id='like' and regexp_like(str,'^a\d+');
STR
-------------
a9999
a9c
 
select str from tmp where id='like' and regexp_like(str,'^a\d+$');
STR
-------------
a9999
```



##### **`regexp_substr`** 

和 `substr` 类似，用于拾取合符正则表达式描述的字符子串，语法如下：

`REGEXP_SUBSTR( string, pattern [, start_position [, nth_appearance [, match_parameter [, sub_expression ] ] ] ] )`

###### `regexp_substr`例子

另一个例子 [oracle中REGEXP_SUBSTR方法的使用](https://blog.csdn.net/u012116457/article/details/50752382)

```plsql
col str format a15;
select
  str,
  regexp_substr(str,'[^,]+')     str,
  regexp_substr(str,'[^,]+',1,1) str,
  regexp_substr(str,'[^,]+',1,2) str,  -- occurrence 第几个匹配组
  regexp_substr(str,'[^,]+',2,1) str   -- position 从第几个字符开始匹配
from tmp
where id='substr';
STR             STR             STR             STR             STR
--------------- --------------- --------------- --------------- ---------------
123,234,345     123             123             234             23
12,34.56:78     12              12              34.56:78        2
123456789       123456789       123456789                       23456789
 
select
  str, 
  regexp_substr(str,'\d')        str,
  regexp_substr(str,'\d+'  ,1,1) str,
  regexp_substr(str,'\d{2}',1,2) str,
  regexp_substr(str,'\d{3}',2,1) str 
from tmp      
where id='substr';
STR             STR             STR             STR             STR
--------------- --------------- --------------- --------------- ---------------
123,234,345     1               123             23              234
12,34.56:78     1               12              34
123456789       1               123456789       34              234
 
 
select regexp_substr('123456789','\d',1,level) str  --取出每位数字，有时这也是行转列的方式
from dual
connect by level<=9
STR
---------------
1
2
3
4
5
6
7
8
9
```

##### **`regexp_instr`** 

和 `instr` 类似，用于标定符合正则表达式的字符子串的开始位置，语法如下：

`REGEXP_INSTR( string, pattern [, start_position [, nth_appearance [, return_option [, match_parameter [, sub_expression ] ] ] ] ] )`

###### `regexp_instr`例子

```plsql
col ind format 9999;
select
  str, 
  regexp_instr(str,'\.'    ) ind ,
  regexp_instr(str,'\.',1,2) ind ,
  regexp_instr(str,'\.',5,2) ind
from tmp where id='instr';
STR               IND   IND   IND
--------------- ----- ----- -----
192.168.0.1         4     8    10
     
select
  regexp_instr('192.168.0.1','\.',1,level) ind ,  -- 点号. 所在的位置
  regexp_instr('192.168.0.1','\d',1,level) ind    -- 每个数字的位置
from dual 
connect by level <=  9
  IND   IND
----- -----
    4     1
    8     2
   10     3
    0     5
    0     6
    0     7
    0     9
    0    11
    0     0
```



##### **`regexp_replace`**

和 `replace` 类似，用于替换符合正则表达式的字符串，语法如下：

`REGEXP_REPLACE( string, pattern [, replacement_string [, start_position [, nth_appearance [, match_parameter ] ] ] ] )`

###### `regexp_replace`例子

```plsql
select
  str,
  regexp_replace(str,'020','GZ') str,
  regexp_replace(str,'(\d{3})(\d{3})','<\2\1>') str -- 将第一、第二捕获组交换位置，用尖括号标识出来
from tmp
where id='replace';  
STR             STR             STR
--------------- --------------- ---------------
(020)12345678   (GZ)12345678    (020)<456123>78
001517729C28    001517729C28    <517001>729C28
```



##### 综合应用的例子

```plsql
`col row_line format a30;``with` `sudoku ``as` `(``  ``select` `'020000080568179234090000010030040050040205090070080040050000060289634175010000020'` `as` `line``  ``from` `dual``),``tmp ``as` `(``  ``select` `regexp_substr(line,``'\d{9}'``,1,``level``) row_line,``  ``level` `col``  ``from` `sudoku``  ``connect` `by` `level``<=9``)``select` `regexp_replace( row_line ,``'(\d)(\d)(\d)(\d)(\d)(\d)(\d)(\d)(\d)'``,``'\1 \2 \3 \4 \5 \6 \7 \8 \9'``) row_line``from` `tmp` `ROW_LINE``------------------------------``0 2 0 0 0 0 0 8 0``5 6 8 1 7 9 2 3 4``0 9 0 0 0 0 0 1 0``0 3 0 0 4 0 0 5 0``0 4 0 2 0 5 0 9 0``0 7 0 0 8 0 0 4 0``0 5 0 0 0 0 0 6 0``2 8 9 6 3 4 1 7 5``0 1 0 0 0 0 0 2 0`
```

# oracle 取出中文字和英文字符

`select regexp_replace('中英文test##','[^' || unistr('\0391') || '-' || unistr('\9FA5') || '0-9A-Za-z]+',' ') from dual`

其中`'^'`是取补集的意思，这里匹配了中英文的补集替换成`' '`

中文编码范围:

```sql
---------------------------------------------------------------------------------- 
|Block                                   | ES6 Range   |   ES5 Range              |
|---------------------------------------------------------------------------------|
|CJK Unified Ideographs                  | 4E00-9FFF   | \u4E00-\u9FFF            |
|CJK Unified Ideographs Extension A      | 3400-4DFF   | \u3400-\u4DFF            |
|CJK Unified Ideographs Extension B      | 20000-2A6DF | \uD840\uDC00-\uD869\uDEDF|
|CJK Unified Ideographs Extension C      | 2A700–2B73F | \uD869\uDF00-\uD86D\uDF3F|
|CJK Unified Ideographs Extension D      | 2B740–2B81F | \uD86D\uDF40-\uD86E\uDC1F|
|CJK Unified Ideographs Extension E      | 2B820–2CEAF | \uD86E\uDC20-\uD873\uDEAF|
|CJK Compatibility Ideographs            | F900-FAFF   | \uF900-\uFAFF            |
|CJK Compatibility Ideographs Supplement | 2F800-2FA1F | \uD87E\uDC00-\uD87E\uDE1F|
----------------------------------------------------------------------------------
```

来自 [stackoverflow中一个问答](https://stackoverflow.com/questions/38477317/how-to-only-filter-chinesespecial-character-set-in-oracle)，不过问题是过滤掉中文字符，这里反过来保留中文字符

