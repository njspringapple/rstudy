
## 原始数据与数据矩阵

数据通常以数据矩阵或表格的形式呈现：
- **组织原则**：整洁数据（Tidy Data，Wickham, 2014）
- 表格及其内容的格式和命名应遵循统一且有记录的**规范**

### 整洁数据 - 标准化的表格结构

一种将数据集的含义映射到其结构的标准方法。（Wickham, 2014）

具体来说：
- **每个特征占用一列**：例如，不要把性别+学年变成一列
- **每个观察单元占用一行**
- 表格中的每个单元格仅包含一个特征值
- **不同类型的观察单元应存放在单独的表格中**

在数据库术语中：符合 **Boyce-Codd 第三范式**（BCNF, 3.5NF）。

### 总结

- **整洁数据的标准结构**意味着：  “整洁的数据集都是相似的……”
    - 列是**变量**，行是**观测值**。
- **但每个混乱的数据集**都有其**独特的混乱方式**：
    - “我的列中既有值也有变量。”
    - “我在一列中有多个变量。”
    - “我的变量既在列中也在行中。”
    - “我甚至不知道我的问题是什么。”
- **使用整洁数据**时：
    - 我们可以使用**相同的工具**，以**相似的方式**处理不同的数据集。
    - 工具和流程更高效、可重复使用。
- **处理不整洁数据**时：
    - 通常需要“重新发明轮子”，使用**一次性的方法**。
    - 这些方法难以迭代或重复使用，效率低下。

## **Tibble**

**tibble** 是 R 编程语言中一种现代化的数据框（data frame）。  它是由 **`tibble` 包**提供的数据结构，用来改进传统数据框的功能和用户体验

### **tibble 的特点**

#### 1. **更清晰的打印输出**

- **tibble** 默认只显示前 10 行和适合屏幕宽度的列数。
- 避免了传统数据框直接打印出大量数据的情况，输出更清爽和易读。

#### 2. **严格的子集操作规则**

- 在 tibble 中，使用 `[[` 或 `$` 提取列时，如果列不存在会抛出错误，而不是像数据框那样返回 `NULL`。
- 这种行为减少了意外错误的发生。

#### 3. **保留原始数据类型**

- tibble 不会自动对字符型数据转换为因子（factor），与数据框的默认行为不同。
- 这使得 tibble 更直观，减少了不必要的类型转换。

#### 4. **兼容性强**

- tibble 是数据框的扩展，完全兼容所有需要数据框的函数，且能与 `dplyr`、`tidyr` 等现代 R 工具链完美结合。

### 创建Tibble

`tibble::as_tibble`：将现有的数据结构转换为 tibble，便于操作且格式一致。  `tibble::tibble`：从头创建一个新的 tibble。

```
data("ChickWeight")  
head(ChickWeight, 3)  
## Grouped Data: weight ~ Time | Chick  
## weight Time Chick Diet  
## 1      42    0     1    1  
## 2      51    2     1    1  
## 3      59    4     1    1  

chickw <- tibble::as_tibble(ChickWeight)  
head(chickw, 3)  
## # A tibble: 3 × 4  
## weight Time Chick Diet  
##  <dbl> <dbl> <ord> <fct>  
## 1     42     0     1     1  
## 2     51     2     1     1  
## 3     59     4     1     1
```

`ChickWeight` 数据集中的这四列分别代表实验中记录的变量。以下是每列的含义：

**`weight`**
- 表示小鸡的体重，单位为 **克**，是一个连续的数值型变量（`<dbl>` 表示双精度数值）。
- 这个变量用于观察小鸡在不同时间点的生长情况。
**`Time`**
- 表示测量小鸡体重的时间点，单位为 **天**（从实验开始的第 0 天开始记录）。
- 这是一个连续的数值型变量，表示每只小鸡的体重是在第几天测量的。
**`Chick`**
- 表示小鸡的编号，是一个**有序因子**（`<ord>`）。
- 每只小鸡都有一个唯一的编号，用于区分不同的小鸡，并跟踪其体重随时间的变化。
- 它是有序的，因为编号表示实验中小鸡的唯一身份标识。
**`Diet`**
- 表示小鸡所喂食的饮食类型，是一个**无序因子**（`<fct>`）。
- 这个变量分为 1、2、3、4 四种饮食组别，对应不同的饲养方案。
- 用于研究不同饮食对小鸡生长的影响。
**总结：**
- `weight` 和 `Time` 是数值型变量，分别表示体重和测量时间。
- `Chick` 是一个有序因子，用于标识小鸡个体，并追踪其生长过程。
- `Diet` 是一个无序因子，用于表示小鸡所喂食的饮食类型，从而研究饮食对体重的影响。

## 数据清洗处理

**创建/修改列：**

- **`dplyr::mutate`**：**添加**新列或**修改**现有列。
```
visits <- visits %>%
  mutate(age = age * 100, 
         income = income * 1e4 * 0.95)
```
- **`dplyr::case_when`**：根据多个条件创建新列。
	- 用于将多个 `if_else()` 语句向量化处理。
	- 每个条件会依次进行评估，对于每个元素，找到的第一个匹配条件将决定输出向量中的对应值。
	- **如果没有条件匹配，可以使用 `TRUE ~ ...` 作为默认的 "else" 语句。**
```
visits <- visits %>%
  mutate(income_cat = case_when(
    income < 2325 ~ "<q25",       # income below the 25% quantile
    income > 8370 ~ ">q75",       # income above the 75% quantile
    TRUE ~ "q25 - q75"           # all remaining cases
  ))

visits %>%
  mutate(income_cat = cut(income, c(0, 2325, 5115, 8370, 14250))) %>% 
  head()
```

**选择列：**

- `dplyr::select`：选择特定的列。
	- `start_with(列名前缀）`、`end_with(列名后缀)`

**选择/移除行：**

- **`dplyr::filter`**：根据逻辑条件筛选行。
- **`dplyr::slice`**：按位置筛选行。
	与 `filter()` 不同，这里不使用逻辑条件，可以与 `group_by()` 结合使用。
```
recidivism %>% 
  slice(1:2) %>%  # 选择数据框的第 1 行和第 2 行
  select(1:10)    # 选择数据框的第 1 列到第 10 列

recidivism %>%
  group_by(arrest) %>%  # 按变量 "arrest" 对数据进行分组
  slice(1:2) %>%        # 在每个分组中选择前两行
  select(1:10)          # 从选定的行中提取第 1 列到第 10 列
```


**计算汇总统计：**

- **`dplyr::summarize`**：将数据汇总到一个表格（数据框或 Tibble）中 。计算一个或多个变量的聚合值，返回一个新的数据框或 Tibble（取决于输入类型），其中仅包含汇总结果。
	- mean：平均值
	- median：中位数
	- quantile：第N个四分位数，例如：**quantile(income, .25), # 计算 income 的第一四分位数 (25%)**
- **`dplyr::count`**：统计数据集中值的出现次数。
```
visits %>% count(gender)
# A tibble: 2 × 2
  gender     n
  <fct>  <int>
1 male    2488
2 female  2702
```

**其他有用的 dplyr 动词：**

- `dplyr::rename`：重命名列以提高清晰度或一致性。
- **`dplyr::arrange`**：用于根据一个或多个列的值对数据框（Data Frames）或 Tibble 的行进行排序。
1. **默认升序：**  
    默认情况下，按照升序排序。  
2. **降序排序：**  
    可以通过 `desc()` 实现降序排序。
3. **多列排序：**  
    支持按照多个列的顺序进行排序。
4. **分组排序：**  
    如果之前使用了 `group_by()`，排序会在每个分组内单独进行，分组结构保持不变。

```
employment_summary <- recidivism_long %>%
  group_by(ID) %>%                             # 按 ID 分组
  summarize(days_employed = sum(employed)) %>% # 计算每个 ID 的就业天数总和
  arrange(days_employed)                       # 按总天数升序排序

employment_summary %>% head(4)                 # 显示前 4 行

recidivism_long %>%
  group_by(ID) %>%                             # 按 ID 分组
  summarize(days_employed = sum(employed)) %>% # 计算每个 ID 的就业天数总和
  arrange(desc(days_employed)) %>%             # 按就业天数降序排序
  head(5)                                      # 显示前 5 行

recidivism_long <- recidivism_long %>%
  group_by(ID) %>%                              # 按 ID 分组，确保累积计算在每个 ID 内单独进行
  arrange(week) %>%                             # 按周排序，确保累积计算的顺序正确
  mutate(cumulative_employment = cumsum(employed)) %>% # 计算累积就业天数
  arrange(ID)                                   # 按 ID 排序回原始顺序

recidivism_long %>% 
  filter(cumulative_employment != 0) %>%        # 筛选累积就业天数不为 0 的行
  select(ID, week, employed, cumulative_employment) %>% # 选择需要的列
  head(4)                                       # 展示前 4 行
```



- **`dplyr::pull`**：提取单列作为向量，类似select。
- **`dplyr::sample_n`**：随机抽取指定数量的样本。
```
set.seed(123456) # Set seed for reproducible results 
recidivism %>% sample_n(2)
```
- `dplyr::sample_frac`：随机抽取指定比例的样本。
- `dplyr::sample`：随机抽取指定数量样本
```
# 从 recidivism 数据集中随机抽取 12 个 ID，不放回抽样，确保抽取的 12 个 `ID` 是唯一的
id_sample <- sample(recidivism$ID, 12, replace = FALSE) 

# 筛选随机抽取的 ID，并绘制累积就业天数随时间变化的趋势图
recidivism_long %>%
  filter(ID %in% id_sample) %>%                    # 筛选随机抽取的 12 个 ID
  ggplot(aes(x = week, y = cumulative_employment)) + # 定义横轴为 week，纵轴为 cumulative_employment
  geom_line() +                                    # 绘制折线图
  facet_wrap(~ID, labeller = "label_both",         # 按 ID 分面显示，每个图显示一个 ID
             nrow = 3L, ncol = 4L) +               # 分面布局为 3 行 4 列
  labs(x = "Weeks since release from prison",      # 设置 x 轴标签
       y = "Cumulative number of days employed")   # 设置 y 轴标签
```

**分组操作：**

- **`dplyr::group_by`：**将一个数据框或 Tibble 按一列或多列分组。可以用多个数据进行分组。
- **`dplyr::ungroup`：取消分组
```
visits %>% group_by(gender) %>% count()

visits %>% group_by(gender) %>% ungroup() %>% count()
```
- **n()函数返回分组大小**

```
v_freq <- visits %>%
    group_by(gender, visits) %>%     # Step 1: Group by gender and visits
    summarize(freq = n()) %>%        # Step 2: Count the number of rows in each group
    mutate(rel_freq = freq/sum(freq)) # Step 3: Calculate the relative frequency
```

```
v_freq %>%
  ggplot(aes(x = factor(visits), y = rel_freq)) +
  geom_bar(stat = "identity", # Use the actual values for the bars
           position = "dodge", # Place bars side by side for different groups
           aes(fill = factor(gender))) # Use gender to color the bars
```

**长/宽格式转换：**

- **宽格式数据**：通常指每一列表示不同的变量或时间点，而行表示观测值。
- **长格式数据**：每一行是一个观测值，变量名称和对应的值存储在两列中，通常被称为 `key` 和 `value`。

- **`tidyr::pivot_longer`**：将宽格式数据转换为长格式数据。
```

```
- `tidyr::pivot_wider`：将长格式数据转换为宽格式数据。

**合并数据集：**

- **内连接**（Inner joins）：`dplyr::inner_join`
- **外连接**（Outer joins）：`dplyr::left_join`、`dplyr::right_join`、`dplyr::full_join`

**集合操作：**

- `dplyr::intersect`：取两个数据集的交集。
- `dplyr::union`：取两个数据集的并集。
- `dplyr::setdiff`：取两个数据集的差集。

**管道操作符（Pipes）**

`dplyr` 使用 `magrittr` 包中的管道操作符 `%>%` 。管道操作符可以提高嵌套函数的可读性。

```
library(tidyr)
library(dplyr)

# 一个简单的正弦函数
f <- function(x, y) {
  y * sin(x - y)
}

# 使用 Base R：
f(f(1, 3), 3)
## [1] 1.581577

# 使用管道操作符：
1 %>% f(3) %>% f(3)
## [1] 1.581577
```

**`%>%` 会将左侧的值作为右侧函数的第一个参数传入**

#### `%>%` 对比 `|>`

一般来说，这两个操作符都可以将操作符左侧的对象传递给操作符右侧函数的第一个参数。

**主要区别：**

- **`|>`**：只能将左侧对象传递给右侧函数的第一个参数。
- **`%>%`**：除了可以传递给第一个参数外，还可以传递给函数的其他任何参数，使用 `.` 来指定位置，**即可以指定参数位置。**

```
text <- "HERE"

# 使用 |> 操作符
text |> paste("is some text.")
## [1] "HERE is some text."

# 使用 %>% 操作符
text %>% paste("is some text.")
## [1] "HERE is some text."

# 使用 %>% 将对象传递给其他参数
text %>% paste("Some text is", .)
## [1] "Some text is HERE"
```

### Base R 对比 Tidyverse

```
head(chickw, 3)
## # A tibble: 3 × 4
## weight Time Chick Diet 
## <dbl>  <dbl> <ord> <fct>
## 1       42     0     1     1 
## 2       51     2     1     1 
## 3       59     4     1     1

dim(chickw)
## [1] 578 4
```

#### filter

```
dim(chickw[chickw$Time %in% 0:10 & chickw$Diet == 1, ])
## [1] 116 4

chickw %>% 
  filter(Time %in% 0:10 & Diet == 1) %>% 
  dim()
## [1] 116 4
```

**`filter()` 是 `dplyr` 包中的一个核心函数，用于基于条件对数据集的行进行筛选。它会返回一个只包含满足条件的行的数据集。**

```
filter(.data, ...)
```
- **`.data`**：数据框或 tibble。
- **`...`**：筛选条件，可以包含 R 的逻辑表达式（如 `==`, `>`, `<`, `%in%` 等）。条件之间可以通过逻辑操作符（`&`, `|`, `!`）组合。
```
# 1. 筛选单个条件
# 筛选出 Diet 等于 1 的行
chickw %>% filter(Diet == 1)

# 2. 筛选多个条件（AND 条件）
# 筛选 Diet 等于 1 且 Time 小于等于 4 的行
chickw %>% filter(Diet == 1 & Time <= 4)

# 3. 筛选多个条件（OR 条件）
# 筛选 Diet 等于 1 或 Time 小于等于 4 的行
chickw %>% filter(Diet == 1 | Time <= 4)

# 4. 使用 %in% 进行匹配
# 筛选 Time 为 0、2、4 的行
chickw %>% filter(Time %in% c(0, 2, 4))

# 5. 使用 ! 进行否定
# 筛选 Diet 不等于 1 的行
chickw %>% filter(Diet != 1)

# 6. 基于多个变量的条件筛选
# 筛选 Diet 为 1 且 weight 大于 50 的行
chickw %>% filter(Diet == 1 & weight > 50)

# 7. 基于字符串匹配
# 筛选 Chick 为 "1" 的行
chickw %>% filter(Chick == "1")
```

#### select

```
# Base R
head(chickw[, c("weight", "Chick", "Diet")], 3)

# dplyr
chickw %>% 
  select(weight, Chick, Diet) %>% 
  head(3)
## # A tibble: 3 × 3
## weight Chick Diet 
## <dbl>  <ord> <fct>
## 1       42     1     1 
## 2       51     1     1 
## 3       59     1     1
```

## 封装函数

```
grp_mean <- function(data, group_var, var) {
  data %>%
    group_by({{ group_var }}) %>% # 按 group_var 分组
    summarize(m = mean({{ var }})) # 计算 var 的分组均值，并将结果命名为 m
}

visits %>% grp_mean(gender, income)

visits %>% grp_mean(income_cat, visits)

```

**使用 `{{ }}` 包裹分组变量和目标变量，这是 tidy evaluation 的写法，用于在函数中动态引用列名。**

```
var_summary <- function(data, var) {
  data %>%
    summarise(n = n(), 
              min = min({{ var }}), 
              max = max({{ var }}))
}

# 示例 1：对整个数据集的 `income` 列进行汇总
visits %>% var_summary(income)
## # A tibble: 1 × 3
##       n   min   max
##   <int> <dbl> <dbl>
## 1   5190     0 14250

# 示例 2：按 `income_cat` 和 `gender` 分组后，对 `visits` 列进行汇总
visits %>%
  group_by(income_cat, gender) %>%
  var_summary(visits)
## # A tibble: 6 × 5
## # Groups:   income_cat [3]
##   income_cat   gender       n   min   max
##   <chr>        <fct>    <int> <dbl> <dbl>
## 1 <q25         male       188     0     8
## 2 <q25         female     255     0     7
## 3 >q75         male       899     0     6
## 4 >q75         female     428     0     8
## 5 q25 - q75    male      1401     0     9
## 6 q25 - q75    female    2019     0     8
```

## 网络数据集下载

```
recidivism <- read.table(
	file = "https://math.unm.edu/~james/Rossi.txt",
	header = TRUE) %>%
	as_tibble()
	
variable.names(recidivism)
```

# 其它实用举例

```
# 移动列
recidivism <- recidivism %>%
  select(ID, everything()) # 将 ID 列移动到最前面

# 添加唯一的 ID 列
recidivism <- recidivism %>%
  mutate(ID = row_number()) # 添加唯一标识符（ID）

# 统计以 "emp" 开头的列数
recidivism %>%
 select(starts_with("emp")) %>%
 ncol()
 
recidivism_long %>%
  filter(ID %in% c(1, 10)) %>%   # 筛选出 ID 为 1 和 10 的数据
  group_by(ID) %>%               # 按 ID 分组
  slice(1:2, (n() - 1):n())      # 提取每组的前两行和最后两行

```

- `everything()` 表示数据框中的所有列
- `row_number()` 为数据框中的每一行生成一个唯一的编号（从 1 开始递增）
- `ncol()` 计算选中的列数