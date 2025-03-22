**绘图**
```
p <- ggplot(data, aes(x = 观察对象列名, y = 观测值列名)) +
  geom_line(aes(color = 数据分类列名)) +  # 折线图
  # geom_point(aes(color = 数据分类列名)) + # 散点图
  
  facet_wrap(~分图列名，nrow = 每行图形数量L, ncol = 每列图形数量L) + 
  scale_color_discrete(name = "图例名称") + # 图例名称
  labs( # 图形描述信息
    title = "Sales and Profit Over Years",     # 图形标题
    subtitle = "Trend of Sales and Profit by Region",  # 副标题
    x = "Year",                               # X轴标题
    y = "Value",                              # Y轴标题
  ) +
  theme_bw() +                                # 简约主题
  theme(
    plot.title = element_text(size = 16, face = "bold"),    # 标题样式
    plot.subtitle = element_text(size = 12, face = "italic") # 副标题样式
    strip.text = element_text(color = "white") # 子图形标题栏字体样式
  )
```

**数据查询/归并**
```
result <- df1 %>%
  rename(原来的列名称 = 新的列名称) %>%                       # 更改列名称，正常查询、归并统计情况不用
  filter(condition_col > 100 & another_col == "value") %>%  # 筛选：满足条件的行
  inner_join(df2, by = c("key_col1" = "key_col2",           # 连接：内连接（inner_join）
                         "key_col3" = "key_col4")) %>%      # 可改为left_join, right_join, full_join
  group_by(group_col1) %>%                                  # 分组：按group_col1分组
  summarise(total = sum(numeric_col, na.rm = TRUE),         # 汇总：计算分组总和
            avg = mean(numeric_col, na.rm = TRUE),          # 汇总：计算分组平均值
            cnt = n()) %>%                                  # 汇总：计算记录总数
  count(需要分类统计次数的列)                                 # 分类汇总，相当于group_by + summarise
  select(列向量)  %>%                                        #
  arrange(desc(total), avg) %>%                             # 排序：按total降序，次按avg升序，可改为
                                                            #arrange(total)（升序）或按多列排序arrange(col1, desc(col2)) 
  ungroup()                                                 # 取消分组，防止后续影响                                               
```
**数据列的修改/新增**
```
df <- df %>%
  mutate_if(is.character, as.factor) %>%
  mutate(
    new_col = case_when(
      col1 > 100 ~ "High",                     # 条件赋值：col1 > 100 时为 "High"
      col1 > 50 ~ "Medium",                    # 条件赋值：50 < col1 <= 100 时为 "Medium"
      TRUE ~ "Low"                             # 默认值：其余情况为 "Low"
    ),
    scaled_col = scale(numeric_col),           # 数值标准化：均值为0，标准差为1
    log_col = log(numeric_col + 1),            # 数值变换：对列取对数（避免log(0))
    date_col = as_date(datetime_col),          # 日期转换：将datetime转为标准日期（lubridate::as_date）
    year = year(datetime_col),                 # 提取年份
    month = month(datetime_col),               # 提取月份（数字）
    day = day(datetime_col),                   # 提取日期
    weekday = wday(datetime_col, label = TRUE),# 提取星期几（如 "Sun", "Mon"）
    combined_col = paste(col2, col3, sep = "_"), # 字符串合并：按 "_" 组合两列
    rank_col = dense_rank(-numeric_col)        # 排名：按numeric_col降序排名（无间断）
  )
```
**字符串处理**
```
df <- df %>%
  mutate(
    lower_col = str_to_lower(col2),            # 转换为小写
    upper_col = str_to_upper(col2),            # 转换为大写
    title_col = str_to_title(col2),            # 转换为标题大小写（首字母大写）
    trimmed_col = str_trim(col2),              # 去除首尾空格
    substring_col = str_sub(col2, 1, 3),       # 提取子串：从第1到第3个字符
    replaced_col = str_replace(col2, "a", "@"),# 替换第一个匹配的"a"为"@"
    replaced_all_col = str_replace_all(col2, "a", "@"), # 替换所有匹配的"a"为"@"
    contains_col = str_detect(col2, "apple"),  # 检测是否包含"apple"（TRUE/FALSE）
    starts_with_col = str_starts(col2, "b"),   # 检测是否以"b"开头（TRUE/FALSE）
    ends_with_col = str_ends(col2, "e")        # 检测是否以"e"结尾（TRUE/FALSE）
  )
```
**正则表达式**
```
df <- df %>%
  mutate(
    contains_digit = str_detect(col2, "\\d"),            # 检测是否包含数字
    starts_with_letter = str_starts(col2, "^[A-Za-z]"), # 检测以字母开头
    ends_with_digit = str_ends(col2, "\\d$"),           # 检测以数字结尾
    extracted_digits = str_extract(col2, "\\d+"),       # 提取第一个连续的数字
    extracted_word = str_extract(col2, "\\b[A-Za-z]+\\b"), # 提取第一个连续的单词
    all_digits = str_extract_all(col2, "\\d+", simplify = TRUE), # 提取所有连续数字
    replaced_non_alpha = str_replace_all(col2, "[^A-Za-z]", "_"), # 将非字母替换为下划线
    removed_digits = str_remove_all(col2, "\\d")         # 删除所有数字
  )
```
**流程控制**
```
process_values <- function(x, y = 3) {
  if (is.null(x) || is.na(y) || !(x >= 1 & x <= 5) ) {
    stop("输入值无效：为空、为 NA 或不在范围内，程序终止！")
  }    
  set.seed(x + y) # 设置随机种子（保证结果可重复）  
  random_value <- sample(1:(x + y), size = 1)  
  # 返回结果
  return(paste("生成的随机数为:", random_value))
}
```
**数据框、向量、列表、因子**
```
names <- c("Alice", "Bob", "Charlie", "David")       # 字符向量
ages <- c(25, 30, 35, 40)                           # 数值向量
genders <- factor(c("F", "M", "M", "F"),            # 因子，手动指定 levels 和 labels
                  levels = c("M", "F"),
                  labels = c("Male", "Female"))
extra_info <- list(hobbies = c("Reading", "Gaming", "Cooking", "Hiking"),  # List
                   scores = c(90, 85, 88, 92))
df <- data.frame(Name = names, Age = ages, Gender = genders, 
                 Hobby = extra_info$hobbies, Score = extra_info$scores)
```

**向量数据访问**

| **操作/数据结构**  | **向量 (vector)**           | **列表 (list)**                  | **数据框 (data.frame)**                              | **矩阵 (matrix)**                      |
| ------------ | ------------------------- | ------------------------------ | ------------------------------------------------- | ------------------------------------ |
| **创建**       | `x <- c(1, 2, 3)`         | `l <- list(a = 1, b = "text")` | `df <- data.frame(a = 1:3, b = c("A", "B", "C"))` | `m <- matrix(1:6, nrow = 2)`         |
|              | `x <- seq(1, 10, by = 2)` | `l <- list(1, "text", TRUE)`   |                                                   | `m <- matrix(0, nrow = 3, ncol = 2)` |
| **访问数据**     | `x[i]`                    | `l$name`, `l[["name"]]`        | `df[i, j]`, `df$col`，pull(列名称)，select(列名称向量)      | `m[i, j]`, `m[ , j]`, `m[i, ]`       |
|              | `x[x > value]`            | `l[[i]]`, `l[i]`               | `df[ , "col"]`, `subset(df, condition)`           |                                      |
| **删除数据/行/列** | `x <- x[-i]`              | `l$name <- NULL`               | `df <- df[-i, ]` (删除行)                            | `m <- m[-i, , drop = FALSE]` (行)     |
|              |                           |                                | `df <- df[ , -j]` (删除列)                           | `m <- m[ , -j, drop = FALSE]` (列)    |
| **添加数据/行/列** | `x <- c(x, new_value)`    | `l$new <- value`               | `df$new_col <- value` (列)                         | `m <- cbind(m, new_col)` (列)         |
|              |                           |                                | `df <- rbind(df, new_row)` (行)                    | `m <- rbind(m, new_row)` (行)         |

**向量操作**

| **操作**     | **函数/方法**                  | **示例代码**                            |
| ---------- | -------------------------- | ----------------------------------- |
| **去重**     | `unique(x)`                | `unique(c(1, 2, 2, 3))`             |
| **排序**     | `sort(x)`                  | `sort(c(3, 1, 2))`                  |
| **降序排序**   | `sort(x, decreasing=TRUE)` | `sort(c(3, 1, 2), decreasing=TRUE)` |
| **获取顺序索引** | `order(x)`                 | `order(c(3, 1, 2))`                 |
| **计数**     | `table(x)`                 | `table(c(1, 2, 2, 3))`              |
| **反转**     | `rev(x)`                   | `rev(c(1, 2, 3))`                   |
| **筛选**     | `x[condition]`             | `x[x > 2]`                          |
| **重复**     | `rep(x, times)`            | `rep(c(1, 2), times=2)`             |
| **生成序列**   | `seq(from, to, by)`        | `seq(1, 10, by=2)`                  |
| **求长度**    | `length(x)`                | `length(c(1, 2, 3))`                |
| **求和**     | `sum(x)`                   | `sum(c(1, 2, 3))`                   |
| **求均值**    | `mean(x)`                  | `mean(c(1, 2, 3))`                  |
| **最大值**    | `max(x)`                   | `max(c(1, 2, 3))`                   |
| **最小值**    | `min(x)`                   | `min(c(1, 2, 3))`                   |
| **标准差**    | `sd(x)`                    | `sd(c(1, 2, 3))`                    |
| **中位数**    | `median(x)`                | `median(c(1, 2, 3))`                |
| **检测 NA**  | `is.na(x)`                 | `is.na(c(1, NA, 3))`                |
| **替换 NA**  | `x[is.na(x)] <- value`     | `x[is.na(x)] <- 0`                  |
| **元素是否存在** | `x %in% y`                 | `c(1, 2) %in% c(2, 3)`              |
**常用数学函数**

| 函数                 | 功能    | 示例                | 结果         | 备注          |
| ------------------ | ----- | ----------------- | ---------- | ----------- |
| **round(x,n)**     | 四舍五入  | round(3.1415,2)   | 3.14       | n表示位数       |
| **floor(x)**       | 向下取整  | floor(3.7)        | 3          | 取小于等于       |
| **ceiling(x)**     | 向上取整  | ceiling(3.2)      | 4          | 取大于等于       |
| **trunc(x)**       | 截断小数  | trunc(3.7)        | 3          | 直接去掉小数      |
| **abs(x)**         | 取绝对值  | abs(-3)           | 3          | 正数不变        |
| **sqrt(x)**        | 平方根   | sqrt(16)          | 4          | x需非负        |
| **log(x)**         | 自然对数  | log(2.718)        | 1          | x需为正        |
| **min/max(x)**     | 最小/大值 | max(1:5)          | 5          | 可多参数        |
| **mean(x)**        | 求均值   | mean(c(1,2,3))    | 2          | na.rm=T处理NA |
| **sum(x)**         | 求和    | sum(1:3)          | 6          | 可多参数        |
| **length(x)**      | 求长度   | length(1:5)       | 5          | 计数用         |
| **range(x)**       | 取值范围  | range(1:5)        | c(1,5)     | 返回最值对       |
| **unique(x)**      | 去重    | unique(c(1,1,2))  | c(1,2)     | 重复值删除       |
| **seq(f,t,by)**    | 生成序列  | seq(1,5,2)        | c(1,3,5)   | from,to,by  |
| **rep(x,n)**       | 重复向量  | rep(1:2,2)        | c(1,2,1,2) | times/each  |
| **is.numeric()**   | 数值检查  | is.numeric(1)     | TRUE       | 类型判断        |
| **is.character()** | 字符检查  | is.character("a") | TRUE       | 类型判断        |
**函数参数检查（Assert方式）**

| 函数名               | 功能      | 示例                                      | 常用参数                                       |
| ----------------- | ------- | --------------------------------------- | ------------------------------------------ |
| **数值检查**          |         |                                         |                                            |
| assert_numeric    | 检查数值向量  | assert_numeric(x, lower=0)              | any.missing, all.missing, min.len, max.len |
| assert_integer    | 检查整数向量  | assert_integer(x, upper=10)             | finite, lower, upper, any.missing          |
| assert_number     | 检查单个数值  | assert_number(x, positive=TRUE)         | na.ok, finite, lower, upper                |
| assert_count      | 检查正整数   | assert_count(x, positive=TRUE)          | na.ok, upper                               |
| **字符检查**          |         |                                         |                                            |
| assert_character  | 检查字符向量  | assert_character(x, pattern="^[A-Z]+$") | min.chars, max.chars, pattern              |
| assert_string     | 检查单个字符串 | assert_string(x, min.chars=1)           | na.ok, pattern, fixed                      |
| **逻辑检查**          |         |                                         |                                            |
| assert_logical    | 检查逻辑向量  | assert_logical(x, any.missing=FALSE)    | any.missing, all.missing                   |
| assert_flag       | 检查单个逻辑值 | assert_flag(x, na.ok=FALSE)             | na.ok                                      |
| **长度检查**          |         |                                         |                                            |
| assert_vector     | 检查向量长度  | assert_vector(x, min.len=1)             | min.len, max.len, any.missing              |
| **范围检查**          |         |                                         |                                            |
| assert_bounded    | 检查数值范围  | assert_bounded(x, lower=0, upper=1)     | lower, upper                               |
| **类型检查**          |         |                                         |                                            |
| assert_class      | 检查对象类型  | assert_class(x, "data.frame")           | exactly                                    |
| **复合检查**          |         |                                         |                                            |
| assert_data_frame | 检查数据框   | assert_data_frame(df, min.rows=1)       | ncols, min.rows, max.rows                  |
| assert_matrix     | 检查矩阵    | assert_matrix(m, min.rows=2)            | mode, rows, cols                           |
| **其他常用**          |         |                                         |                                            |
| assert_subset     | 检查子集关系  | assert_subset(x, choices=letters)       | empty.ok                                   |
| assert_names      | 检查名称    | assert_names(x, type="unique")          | subset.of, must.include                    |

**match.arg**

| 用法                                | 说明       | 示例                                                                                     |
| --------------------------------- | -------- | -------------------------------------------------------------------------------------- |
| match.arg(arg)                    | 从默认选项中匹配 | function(method = c("pearson", "kendall", "spearman")) { method <- match.arg(method) } |
| match.arg(arg, choices)           | 从给定选项中匹配 | match.arg(x, c("red", "blue", "green"))                                                |
| match.arg(arg, several.ok = TRUE) | 允许多个匹配   | match.arg(c("red", "blue"), choices, several.ok = TRUE)                                |
