# PHP常用函数

## 字符串 String

### 比较

| 函数名 | 描述 |
| --- | --- |
| [strcmp](https://www.php.net/strcmp) | 该函数是二进制安全的，且对大小写敏感 |
| [strncmp](https://www.php.net/strncmp) | 前 n 个字符的字符串比较（对大小写敏感） |
| [strcasecmp](https://www.php.net/strcasecmp) | 比较两个字符串，对大小写不敏感 |
| [strncasecmp](https://www.php.net/strncasecmp) | 前 n 个字符的字符串比较（对大小写不敏感） |
| [strnatcmp](https://www.php.net/strnatcmp) | 使用一种 “自然” 算法来比较两个字符串（对大小写敏感） |
| [strnatcasecmp](https://www.php.net/strnatcasecmp) | 使用一种 “自然” 算法来比较两个字符串（对大小写不敏感） |
| [substr_compare](https://www.php.net/substr_compare) | 从指定的开始长度比较两个字符串 |
| [strcoll](https://www.php.net/strcoll) | 该函数不是二进制安全的，对大小写敏感；字符串的比较会根据本地设置而变化。（A<a 或="" a="">a）</a> |

### 修改

| 函数名 | 描述 |
| --- | --- |
| [str_repeat](https://www.php.net/str_repeat) | 把字符串重复指定的次数 |
| [strrev](https://www.php.net/strrev) | 反转字符串 |
| [str_shuffle](https://www.php.net/str_shuffle) | 随机地打乱字符串中的所有字符 |
| [wordwrap](https://www.php.net/wordwrap) | 按照指定长度对字符串进行折行处理 |
| [chunk_split](https://www.php.net/chunk_split) | 把字符串分割为一连串更小的部分 |
| [money_format](https://www.php.net/money_format) | 把字符串格式化为货币字符串 |
| [number_format](https://www.php.net/number_format) | 通过千位分组来格式化数字 |
| [str_pad](https://www.php.net/str_pad) | 把字符串填充为指定的长度 |
| [strtolower](https://www.php.net/strtolower) | 把字符串转换为小写 |
| [strtoupper](https://www.php.net/strtoupper) | 把字符串转换为大写 |
| [lcfirst](https://www.php.net/lcfirst) | 把字符串中的首字符转换为小写 |
| [ucfirst](https://www.php.net/ucfirst) | 把字符串中的首字符转换为大写 |
| [ucwords](https://www.php.net/ucwords) | 把字符串中每个单词的首字符转换为大写 |
| [strtr](https://www.php.net/strtr) | 转换字符串中特定的字符 |
| [str_replace](https://www.php.net/str_replace) | 使用一个字符串替换字符串中的另一些字符（对大小写敏感） |
| [str_ireplace](https://www.php.net/str_ireplace) | 替换字符串中的一些字符。（对大小写不敏感） |
| [substr_replace](https://www.php.net/substr_replace) | 函数把字符串的一部分替换为另一个字符串 |
| [hebrev](https://www.php.net/hebrev) | 把希伯来逻辑文本转换为希伯来可见文本 |
| [hebrevc](https://www.php.net/hebrevc) | 把希伯来逻辑文本转换为希伯来可见文本，把新行 (`\n`) 转换为 `<br />` |

### 插入

| 函数名 | 描述 |
| --- | --- |
| [addslashes](https://www.php.net/addslashes) | 在指定的预定义字符前添加反斜杠，可用于为存储在数据库中的字符串以及数据库查询语句准备合适的字符串 |
| [stripslashes](https://www.php.net/stripslashes) | 删除由 `addslashes()` 函数添加的反斜杠 |
| [addcslashes](https://www.php.net/addcslashes) | 在指定的字符前添加反斜杠 |
| [stripcslashes](https://www.php.net/stripcslashes) | 删除由 `addcslashes()` 函数添加的反斜杠 |
| [quotemeta](https://www.php.net/quotemeta) | 在字符串中某些预定义的字符前添加反斜杠 |

### 删除

| 函数名 | 描述 |
| --- | --- |
| [trim](https://www.php.net/trim) | 从字符串的两端删除空白字符和其他预定义字符 |
| [chop](https://www.php.net/chop) | `rtrim()` 的别名 |
| [ltrim](https://www.php.net/ltrim) | 从字符串左侧删除空格或其他预定义字符 |
| [rtrim](https://www.php.net/rtrim) | 从字符串的末端开始删除空白字符或其他预定义字符 |

### 子字符串

| 函数名 | 描述 |
| --- | --- |
| [substr](https://www.php.net/substr) | 返回字符串的一部分 |
| [strrchr](https://www.php.net/strrchr) | 查找字符串在另一个字符串中最后一次出现的位置，并返回从该位置到字符串结尾的所有字符 |
| [strtok](https://www.php.net/strtok) | 按需切割字串，不可以用中文切割会乱码 |

### 搜索

| 函数名 | 描述 |
| --- | --- |
| [strchr](https://www.php.net/strchr) | `strstr` 的别名 |
| [strstr](https://www.php.net/strstr) | 搜索一个字符串在另一个字符串中的第一次出现，返回的是字符串的其余部分（从匹配点），对大小写敏感 |
| [stristr](https://www.php.net/stristr) | 搜索一个字符串在另一个字符串中的第一次出现，返回的是字符串的其余部分（从匹配点），对大小写不敏感 |
| [strpos](https://www.php.net/strpos) | 返回字符串在另一个字符串中第一次出现的位置， 返回匹配点位置，对大小写敏感 |
| [stripos](https://www.php.net/stripos) | 返回字符串在另一个字符串中第一次出现的位置， 返回匹配点位置，对大小写不敏感 |
| [strrpos](https://www.php.net/strrpos) | 返回字符串在另一个字符串中最后一次出现的位置， 返回匹配点位置，对大小写敏感 |
| [strripos](https://www.php.net/strripos) | 返回字符串在另一个字符串中最后一次出现的位置， 返回匹配点位置，对大小写不敏感 |
| [strpbrk](https://www.php.net/strpbrk) | 在字符串中搜索指定字符中的任意一个，对大小写敏感 |

### 与数组相关

| 函数名 | 描述 |
| --- | --- |
| [join](https://www.php.net/join) | `implode()` 的别名 |
| [implode](https://www.php.net/implode) | 把数组元素组合为一个字符串 |
| [explode](https://www.php.net/explode) | 把字符串打散为数组 |
| [str_split](https://www.php.net/str_split) | 把字符串分割到数组中 |

### 解析

| 函数名 | 描述 |
| --- | --- |
| [parse_str](https://www.php.net/parse_str) | 把查询字符串解析到变量中 |
| [sscanf](https://www.php.net/sscanf) | 根据指定的格式解析来自一个字符串的输入 |
| [str_getcsv](https://www.php.net/str_getcsv) | 解析 `CSV` 格式字段的字符串，并返回一个包含所读取字段的数组 |

### 与 Html 相关

| 函数名 | 描述 |
| --- | --- |
| [html_entity_decode](https://www.php.net/html_entity_decode) | 是 `htmlentities()` 的反函数，把 `HTML` 实体转换为字符 |
| [htmlentities](https://www.php.net/htmlentities) | 把字符转换为 `HTML` 实体 |
| [htmlspecialchars_decode](https://www.php.net/htmlspecialchars_decode) | 把一些预定义的 `HTML` 实体转换为字符 |
| [htmlspecialchars](https://www.php.net/htmlspecialchars) | 把一些预定义的字符转换为 `HTML` 实体 |
| [nl2br](https://www.php.net/nl2br) | 在字符串中的每个新行 (`\n`) 之前插入 `HTML` 换行符 (`<br />`) |
| [strip_tags](https://www.php.net/strip_tags) | 剥去 `HTML`、`XML` 以及 `PHP` 的标签 |

### 与 ASCII 相关

| 函数名 | 描述 |
| --- | --- |
| [ord](https://www.php.net/ord) | 返回字符串第一个字符的 `ASCII` 值 |
| [chr](https://www.php.net/chr) | 从指定的 `ASCII` 值返回字符 |

### 长度、计算

| 函数名 | 描述 |
| --- | --- |
| [strlen](https://www.php.net/strlen)  | 返回字符串的长度 |
| [str_word_count](https://www.php.net/str_word_count)  | 计算字符串中的单词数 |
| [substr_count](https://www.php.net/substr_count)  | 计算子串在字符串中出现的次数 |
| [count_chars](https://www.php.net/count_chars)  | 返回字符串所用字符的信息 |
| [strcspn](https://www.php.net/strcspn)  | 返回在找到任何指定的字符之前，在字符串查找的字符数 |
| [strspn](https://www.php.net/strspn)  | 返回在字符串中包含 `charlist` 参数中指定字符的数目 |
| [md5_file](https://www.php.net/md5_file)  | 计算文件的 `MD5` 散列 |
| [md5](https://www.php.net/md5)  | 计算字符串的 `MD5` 散列 |
| [crc32](https://www.php.net/crc32)  | 计算一个字符串的 `crc32` 多项式，该函数可用于验证数据的完整性 |
| [sha1_file](https://www.php.net/sha1_file)  | 计算文件的 `SHA-1` 散列 |
| [sha1](https://www.php.net/sha1)  | 计算字符串的 `SHA-1` 散列 |
| [soundex](https://www.php.net/soundex)  | 计算字符串的 `soundex` 键，为发音相似的单词创建相同的键 |
| [metaphone](https://www.php.net/metaphone)  | 计算字符串的 `metaphone` 键，`metaphone` 键字符串的英语发音 |
| [levenshtein](https://www.php.net/levenshtein)  | 返回两个字符串之间的 `Levenshtein` 距离 |
| [similar_text](https://www.php.net/similar_text)  | 计算两个字符串的匹配字符的数目 |

### 输出

| 函数名 | 描述 |
| --- | --- |
| [echo](https://www.php.net/echo) | 同`printf`，比`printf`快【`echo(strings)`】 |
| [print](https://www.php.net/print) | 输出一个字符串【`print()` 函数实际上不是函数，所以不必对它使用括号】【 `print(strings)`】 |
| [printf](https://www.php.net/printf) | 输出格式化的字符串 【`printf(format,arg1,arg2,arg++)`】 |
| [vprintf](https://www.php.net/vprintf) | 输出格式化的字符串，与`printf`不同的是第二参数为数组【`vprintf(format,argarray)`】 |
| [sprintf](https://www.php.net/sprintf) | 返回格式化的字符串【`sprintf(format,arg1,arg2,arg++)`】 |
| [vsprintf](https://www.php.net/vsprintf) | 返回格式化的字符串，与`sprintf`不同的是第二参数为数组【`vsprintf(format,argarray)`】 |
| [fprintf](https://www.php.net/fprintf) | 把格式化的字符串写到指定的输出流【`fprintf(stream,format,arg1,arg2,arg++)`】 |
| [vfprintf](https://www.php.net/vfprintf) | 把格式化的字符串写到指定的输出流【`vfprintf(stream,format,argarray)`】 |

### 编码解码、校验

| 函数名 | 描述 |
| --- | --- |
| [crypt](https://www.php.net/crypt) | 单向加密 |
| [str_rot13](https://www.php.net/str_rot13) | 对字符串执行 `ROT13` 编码 |
| [convert_uudecode](https://www.php.net/convert_uudecode) | 对 `uuencode` 编码的字符串进行解码 |
| [convert_uuencode](https://www.php.net/convert_uuencode) | 使用 `uuencode` 算法对字符串进行编码 |
| [quoted_printable_decode](https://www.php.net/quoted_printable_decode) | 把 `quoted-printable` 字符串解码为 8 位 `ASCII` 字符串 |
| [quoted_printable_encode](https://www.php.net/quoted_printable_encode) | 把 8 位字符串转换为 `quoted-printable` 字符串 |
| [bin2hex](https://www.php.net/bin2hex) | 将二进制数据转换成十六进制表示 |
| [hex2bin](https://www.php.net/hex2bin) | 把十六进制值转换为 `ASCII` 字符 |

### 配置信息

| 函数名 | 描述 |
| --- | --- |
| [nl_langinfo](https://www.php.net/nl_langinfo) | 返回指定的本地信息 |
| [setlocale](https://www.php.net/setlocale) | 设置地区信息（地域信息） |
| [localeconv](https://www.php.net/localeconv) | 包含本地数字及货币信息格式的数组 |
| [get_html_translation_table](https://www.php.net/get_html_translation_table) | 返回被 `htmlentities()` 和 `htmlspecialchars()` 函数使用的翻译表 |

## 数组 Array

### 新建

| 函数名 | 描述 |
| --- | --- |
| [array](https://www.php.net/array) | 新建一个数组 |
| [compact](https://www.php.net/compact) | 建立一个数组，包括变量名和它们的值 |
| [array_rand](https://www.php.net/array_rand) | 返回给定数组中的随机键名 |
| [array_fill](https://www.php.net/array_fill) | 用给定的值填充数组 |
| [array_fill_keys](https://www.php.net/array_fill_keys) | 用给定的指定键名的键值填充数组 |
| [array_pad](https://www.php.net/array_pad) | 指定数量的带有指定值的元素插入到数组中 |
| [range](https://www.php.net/range) | 创建一个包含指定范围的元素的数组 |
| [array_combine](https://www.php.net/array_combine) | 通过合并两个数组来创建一个新数组，其中的一个数组元素为键名，另一个数组元素为键值 |
| [array_column](https://www.php.net/array_column) | 返回数组中指定的一列 |
| [array_chunk](https://www.php.net/array_chunk) | 把数组分割为新的数组块，其中每个数组的单元数目由 `size` 参数决定 |

### 返回新数组

| 函数名 | 描述 |
| --- | --- |
| [array_slice](https://www.php.net/array_slice) | 数组中根据条件取出一段值，并返回 (`key`为数字，删；非数字，保留) |
| [array_splice](https://www.php.net/array_splice) | 从数组中移除选定的元素，并用新元素取代它。该函数也将返回包含被移除元素的数组 (删) |

### 赋值

| 函数名 | 描述 |
| --- | --- |
| [list](https://www.php.net/list) | 把数组中的值赋给一些变量 |
| [extract](https://www.php.net/extract) | 使用数组键名作为变量名，使用数组键值作为变量值 |

### 键或值

| 函数名 | 描述 |
| --- | --- |
| [array_keys](https://www.php.net/array_keys) | 返回包含数组中所有键名的一个新数组 |
| [array_key_exists](https://www.php.net/array_key_exists) | 检查某个数组中是否存在指定的键名，如果键名存在则返回 `true` |
| [key_exists](https://www.php.net/key_exists) | `array_key_exists` 函数的别名 |
| [array_change_key_case](https://www.php.net/array_change_key_case) | 将数组的所有的键转换为大写字母 |
| [array_replace](https://www.php.net/array_replace) | 根据`Key`, 使用后面数组的值替换第一个数组的值 |
| [array_replace_recursive](https://www.php.net/array_replace_recursive) | 递归地使用后面数组的值替换第一个数组的值 |
| [array_values](https://www.php.net/array_values) | 返回一个包含给定数组中所有键值的数组，但不保留键名 |
| [array_unique](https://www.php.net/array_unique) | 移除数组中的重复的值，并返回结果数组，返回的数组中键名不变 |
| [array_flip](https://www.php.net/array_flip) | 返回一个键值反转后的数组，如果同一值出现了多次，则最后一个键名将作为它的值，所有其他的键名都将丢失 |

### 搜索

| 函数名 | 描述 |
| --- | --- |
| [in_array](https://www.php.net/in_array) | 搜索数组中是否存在指定的值 |
| [array_search](https://www.php.net/array_search) | 在数组中搜索某个键值，并返回对应的键名 |

### 进出

| 函数名 | 描述 |
| --- | --- |
| [array_shift](https://www.php.net/array_shift) | 删除数组中第一个元素，并返回被删除元素的值 (`key`为数字，删；非数字，保留) |
| [array_unshift](https://www.php.net/array_unshift) | 向数组插入新元素。新数组的值将被插入到数组的开头 (`key`为数字，删；非数字，保留) |
| [array_pop](https://www.php.net/array_pop) | 删除数组中的最后一个元素 |
| [array_push](https://www.php.net/array_push) | 向第一个参数的数组尾部添加一个或多个元素（入栈），然后返回新数组的长度 |

### 排序

| 函数名 | 描述 |
| --- | --- |
| [shuffle](https://www.php.net/shuffle) | 把数组中的元素按随机顺序重新排列 (删除`Key`) |
| [array_reverse](https://www.php.net/array_reverse) | 返回一个单元顺序相反的数组 (可保留，可删除，由第二参数决定) |
| [sort](https://www.php.net/sort) | 按升序对给定数组的值排序 (删除`Key`) |
| [rsort](https://www.php.net/rsort) | 按降序对给定数组的值排序 (删除`Key`) |
| [asort](https://www.php.net/asort) | 对关联数组按照键值进行升序排序 (保留`Key`) |
| [arsort](https://www.php.net/arsort) | 对关联数组按照键值进行降序排序 (保留`Key`) |
| [ksort](https://www.php.net/ksort) | 对关联数组按照键名进行升序排序 (保留`Key`) |
| [krsort](https://www.php.net/krsort) | 对关联数组按照键名进行降序排序 (保留`Key`) |
| [natsort](https://www.php.net/natsort) | 用” 自然排序” 算法对数组进行排序 (保留`Key`) |
| [natcasesort](https://www.php.net/natcasesort) | 用 “自然排序” 算法对数组进行不区分大小写字母的排序(保留`Key`) |
| [usort](https://www.php.net/usort) | 使用用户自定义的比较函数对数组中的元素进行排序 (删除`Key`) |
| [uksort](https://www.php.net/uksort) | 使用用户自定义的比较函数对数组 中的元素按键名进行排序 (保留`Key`) |
| [uasort](https://www.php.net/uasort) | 使用用户自定义的比较函数对数组中的值进行排序并保持索引关联 (保留`Key`) |

### 交集和差集， 合并

| 函数名 | 描述 |
| --- | --- |
| [array_diff](https://www.php.net/array_diff) | 返回所有在被比较的数组中，但是不在任何其他参数数组中的键值。（保留`key`） |
| [array_udiff](https://www.php.net/array_udiff) | 比较两个数组的键值（使用用户自定义函数比较键值），并返回差集 |
| [array_diff_key](https://www.php.net/array_diff_key) | 比较键值，返差集；（保留`key`） |
| [array_diff_ukey](https://www.php.net/array_diff_ukey) | 用户定义的比较函数比较键值，返差集；（保留`key`） |
| [array_diff_assoc](https://www.php.net/array_diff_assoc) | 返回所有在被比较的数组中，但是不在任何其他参数数组中的键和值（保留`key`） |
| [array_udiff_assoc](https://www.php.net/array_udiff_assoc) | 同`diff_assoc`比，多了自定义回调函数（参数为键值） |
| [array_diff_uassoc](https://www.php.net/array_diff_uassoc) | 相比`diff_assoc`，使用的是用户自定义的比较函数（参数为键名） |
| [array_udiff_uassoc](https://www.php.net/array_udiff_uassoc) | 相比`diff_uassoc`，多了两个回调函数，分别用于键名和键值的比较 |
| [array_intersect](https://www.php.net/array_intersect) | 比较两个（或更多个）数组的键值，并返回交集数组（保留被比较数组的`key`） |
| [array_uintersect](https://www.php.net/array_uintersect) | 用自定义回调函数（参数为键值）比较交集 |
| [array_intersect_key](https://www.php.net/array_intersect_key) | 比较键名计算数组的交集（保留被比较数组的`key`） |
| [array_intersect_ukey](https://www.php.net/array_intersect_ukey) | 用回调函数比较键名来计算数组的交集（保留被比较数组的`key`） |
| [array_intersect_assoc](https://www.php.net/array_intersect_assoc) | 比较两个数组的键名和键值，并返回交集 |
| [array_uintersect_assoc](https://www.php.net/array_uintersect_assoc) | 同`intersect_assoc`比，多了自定义回调函数（参数为键值） |
| [array_intersect_uassoc](https://www.php.net/array_intersect_uassoc) | 用回调函数比较两个数组的键名和键值，并返回交集 |
| [array_uintersect_uassoc](https://www.php.net/array_uintersect_uassoc) | 相比`intersect_uassoc`，多了两个回调函数，分别用于键名和键值的比较 |
| [array_merge](https://www.php.net/array_merge) | 把一个或多个数组合并为一个数组 (`key`为数字，重新排序；非数字，替换) |
| [array_merge_recursive](https://www.php.net/array_merge_recursive) | `array_merge_recursive()`不会进行键名覆盖，而是将多个相同键名的值递归组成一个数组 |

### 数学运算

| 函数名 | 描述 |
| --- | --- |
| [count](https://www.php.net/count) | 返回数组中元素的数目 |
| [sizeof](https://www.php.net/sizeof) | 同`count` |
| [array_sum](https://www.php.net/array_sum) | 返回数组中所有值的和 |
| [array_count_values](https://www.php.net/array_count_values) | 统计数组中所有的值出现的次数，数组中的值作为键名，出现的次数作为值 |
| [array_product](https://www.php.net/array_product) | 计算数组中所有值的乘积 |
| [array_map](https://www.php.net/array_map) | 将函数作用到数组中的每个值上，每个值都乘以本身，并返回带有新值的数组 |

### 用户自定义函数

| 函数名 | 描述 |
| --- | --- |
| [array_walk](https://www.php.net/array_walk) | 对数组中的每个元素应用用户自定义函数 |
| [array_walk_recursive](https://www.php.net/array_walk_recursive) | 该函数与 `array_walk()`函数的不同在于可以操作更深的数组（一个数组中包含另一个数组） |
| [array_reduce](https://www.php.net/array_reduce) | 向用户自定义函数发送数组中的值，并返回一个字符串 |
| [array_filter](https://www.php.net/array_filter) | 用回调函数过滤数组中的单元 (数组的键名保留不变) |

## Math

### 常用计算

| 函数名 | 描述 |
| --- | --- |
| [min](https://www.php.net/min) | 找出最小值 |
| [max](https://www.php.net/max) | 找出最大值 |
| [abs](https://www.php.net/abs) | 绝对值 |
| [round](https://www.php.net/round) | 对浮点数进行四舍五入 |
| [ceil](https://www.php.net/ceil) | 返回大于或者等于指定表达式的最小整数，天花板函数 |
| [floor](https://www.php.net/floor) | 返回小于或者等于指定表达式的最大整数，地板函数 |
| [intdiv](https://www.php.net/intdiv) | 对除法结果取整，返回商 |
| [fmod](https://www.php.net/fmod) | 返回除法的浮点数余数，返回余数 |
| [is_nan](https://www.php.net/is_nan) | 判断是否为合法数值 |
| [hypot](https://www.php.net/hypot) | 计算一直角三角形的斜边长度 |
| [sqrt](https://www.php.net/sqrt) | 平方根 |

### 进制转换

| 函数名 | 描述 |
| --- | --- |
| [base_convert](https://www.php.net/base_convert) | 在任意进制之间转换数字 |
| [decbin](https://www.php.net/decbin) | 十进制转换为二进制 |
| [bindec](https://www.php.net/bindec) | 二进制转换为十进制 |
| [decoct](https://www.php.net/decoct) | 十进制转换为八进制 |
| [octdec](https://www.php.net/octdec) | 八进制转换为十进制 |
| [dechex](https://www.php.net/dechex) | 十进制转换为十六进制 |
| [hexdec](https://www.php.net/hexdec) | 十六进制转换为十进制 |

### 随机数

| 函数名 | 描述 |
| --- | --- |
| [lcg_value](https://www.php.net/lcg_value) | 组合线性同余发生器，返回范围为 (0, 1) 的一个伪随机数 |
| [rand](https://www.php.net/rand) | 产生一个随机整数，如果没有提供可选参数 `min` 和 `max`，`rand()` 返回 0 到 `RAND_MAX` 之间的伪随机整数。 |
| [mt_rand](https://www.php.net/mt_rand) | 生成更好的随机整数 |
| [getrandmax](https://www.php.net/getrandmax) | 显示随机数最大的可能值 |
| [mt_getrandmax](https://www.php.net/mt_getrandmax) | 显示随机数的最大可能值 |
| [srand](https://www.php.net/srand) | 播下随机数发生器种子，自 `PHP 4.2.0` 起，不再需要用 `srand()` 或 `mt_srand()` 函数给随机数发生器播种，现在已自动完成 |
| [mt_srand](https://www.php.net/mt_srand) | 播下一个更好的随机数发生器种子 (`Mersenne Twister`) |

## Directory

### 文件夹，获取目录信息

| 函数名 | 描述 |
| --- | --- |
| [dir](https://www.php.net/dir) | 打开一个目录句柄，并返回一个对象。这个对象包含三个方法：`read()` , `rewind()` 以及 `close()` |
| [opendir](https://www.php.net/opendir) | 打开一个目录句柄，，并返回该句柄。可由 `closedir()`，`readdir()` 和 `rewinddir()` 使用 |
| [rewinddir](https://www.php.net/rewinddir) | 倒回目录句柄 |
| [getcwd](https://www.php.net/getcwd) | 取得当前工作目录 |
| [scandir](https://www.php.net/scandir) | 列出指定路径中的文件和目录 |
| [chdir](https://www.php.net/chdir) | 把当前的目录改变为指定的目录。若成功，则该函数返回 `true`，否则返回 `false` |
| [chroot](https://www.php.net/chroot) | 把当前进程的根目录改变为指定的目录。若成功，则该函数返回 `true`，否则返回 `false` |

## FileSystem

### 创建

| 函数名 | 描述 |
| --- | --- |
| [mkdir](https://www.php.net/mkdir) | 新建目录 |

### 删除

| 函数名 | 描述 |
| --- | --- |
| [unlink](https://www.php.net/unlink) | 删除文件 |
| [rmdir](https://www.php.net/rmdir) | 删除目录 |
| [delete](https://www.php.net/delete) | 参见 `unlink` 或 `unset` |

## 判断

| 函数名 | 描述 |
| --- | --- |
| [is_dir](https://www.php.net/is_dir) | 判断给定文件名是否是一个目录 |
| [is_file](https://www.php.net/is_file) | 判断给定文件名是否为一个正常的文件 |
| [file_exists](https://www.php.net/file_exists) | 检查文件或目录是否存在 |

## 时间 time

### 获取时间（时间戳）

| 函数名 | 描述 |
| --- | --- |
| [time](https://www.php.net/time) | 返回当前时间的 `Unix` 时间戳 |
| [microtime](https://www.php.net/microtime) | 返回当前 `Unix` 时间戳和微秒数 |
| [mktime](https://www.php.net/mktime) | 返回一个日期的 `Unix` 时间戳 |
| [gmmktime](https://www.php.net/gmmktime) | 返回 `GMT` 日期的 `UNIX` 时间戳 |
| [date_timestamp_get](https://www.php.net/date_timestamp_get) | 返回今天的日期和时间的 `Unix` 时间戳 |
| [strtotime](https://www.php.net/strtotime) | 将任何英文文本的日期或时间描述解析为 `Unix` 时间戳 |

### 获取时间（非时间戳）

| 函数名 | 描述 |
| --- | --- |
| [localtime](https://www.php.net/localtime) | 返回本地时间（一个数组存放关于时间的各项信息） |
| [getdate](https://www.php.net/getdate) | 返回一个根据 `timestamp` 得出的包含有日期信息的结合数组。如果没有给出时间戳，则认为是当前本地时间 |
| [gettimeofday](https://www.php.net/gettimeofday) | 返回一个包含当前时间信息的数组 |
| [strptime](https://www.php.net/strptime) | 解析由 `strftime()` 生成的时间 / 日期 |
| [date](https://www.php.net/date) | 格式化一个本地时间／日期 |
| [gmdate](https://www.php.net/gmdate) | 格式化 `GMT`/`UTC` 日期和时间，并返回格式化的日期字符串 |
| [idate](https://www.php.net/idate) | 将本地时间 / 日期格式化为整数，与 `date()` 不同，`idate()` 只接受一个字符作为 `format` 参数 |
| [date_format](https://www.php.net/date_format) | 返回一个根据指定格式进行格式化的日期 |
| [date_interval_format](https://www.php.net/date_interval_format) | 用于格式化时间间隔 |

### 时间加减

| 函数名 | 描述 |
| --- | --- |
| [date_add](https://www.php.net/date_add)  | 添加日、月、年、时、分和秒到一个日期 |
| [date_sub](https://www.php.net/date_sub)  | 从指定日期减去日、月、年、时、分和秒 |
| [date_modify](https://www.php.net/date_modify)  | 修改时间戳 |
| [date_diff](https://www.php.net/date_diff)  | 返回两个 `DateTime` 对象间的差值 |

> [原文](https://github.com/fossabot/notes-1/blob/master/PHP/PHP%E5%B8%B8%E7%94%A8%E5%87%BD%E6%95%B0.md)