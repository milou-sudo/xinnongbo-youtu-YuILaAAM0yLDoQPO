
之前介绍过两个数据展示的组件，`st.dataframe`和`st.table`。


今天介绍的`st.data_editor`组件，除了展示数据的功能更加强大之外，还可以编辑数据。


# 1\. 概要


`st.data_editor`组件在数据展示和编辑中都发挥着独特且重要的作用。


首先，在数据展示方面，它的优势在于：


1. **直观性**：以表格形式展示数据，使得数据更加直观易懂。通过该组件可以方便地查看数据集的整体结构和细节
2. **适应性**：能够自动适应屏幕宽度，并支持水平或垂直滚动，确保用户能方便地浏览整个数据集
3. **交互性**：支持对数据进行排序、筛选和搜索等操作，增强了数据的可读性和交互性


在数据编辑方面，优势也很明显：


1. **灵活性**：支持类电子表格编辑，可以在界面上直接对数据进行增删改操作，无需通过代码实现，提高了数据编辑的灵活性
2. **定制性**：提供了丰富的列定制选项，如通过`st.column_config`可以设置列的标题、类型、格式以及编辑属性（如最小/最大值或步长）等，以满足不同场景下的数据编辑需求
3. **多数据类型支持**：支持多种数据类型的数据编辑，包括数值、文本、日期、时间等，使得数据编辑更加全面和便捷
4. **状态管理**：具有状态管理机制，可以记录用户的编辑操作，并在必要时进行回滚或提交。这使得数据编辑过程更加安全和可控


# 2\. 基本用法


`st.data_editor`组件多用在需要实时编辑数据的情况，它主要参数有：




| **名称** | **类型** | **说明** |
| --- | --- | --- |
| data | \- | 任何适合渲染成表格的数据类型 |
| width | int | 数据编辑器的宽度，单位 px |
| height | int | 数据编辑器的高度，单位 px |
| use\_container\_width | bool | 是否使用父容器宽度 |
| hide\_index | bool | 是否隐藏索引列 |
| column\_order | \[str] | 指定列的显示顺序 |
| column\_config | dict | 配置列的显示方式，例如标题、可见性、类型或格式，以及编辑属性等等 |
| num\_rows | str | 控制用户是否可以在数据编辑器中添加和删除行 |
| disabled | bool 或 \[str] | 是否禁用编辑 |
| key | str | 组件名称，具有唯一性 |


这里重点关注两个参数，`data`和`column_config`。


`data`这个参数支持的数据类型非常宽泛，具体包括：`pandas.DataFrame`, `pandas.Series`, `pandas.Styler`, `pandas.Index`, `pyarrow.Table`, `numpy.ndarray`, `pyspark.sql.DataFrame`, `snowflake.snowpark.DataFrame`, `list`, `set`, `tuple`, `dict`等等。


几乎所有常用的存储数据的结构都包含了。


`column_config`这个参数可以高度定制列的显示和编辑行为，极大的增强了`st.data_editor`的能力，


在后面的高级用法中会详细介绍。


## 2\.1\. 使用示例


下面通过一个根据实际应用简化的示例来演示`st.data_editor`基本用法。


我们构建一个简单的产品库存管理系统，通过一个界面来展示和编辑产品的库存数据。


上面用`st.data_editor`来编辑表格数据，下面用`st.dataframe`同步显示编辑后的数据。



```
import streamlit as st
import pandas as pd

# 模拟产品库存数据
inventory_data = pd.DataFrame(
    {
        "Product ID": [1, 2, 3],
        "Product Name": ["Apple", "Banana", "Cherry"],
        "Quantity": [100, 150, 200],
        "Price": [0.5, 0.3, 1.0],
    }
)

# 使用st.data_editor展示和编辑库存数据
st.title("产品库存管理系统")
edited_inventory = st.data_editor(
    inventory_data,
    width=600,
    height=300,
    num_rows="dynamic",
)

# 显示编辑后的数据（可选，此处仅为展示效果）
st.write("编辑后的库存数据：")
st.dataframe(edited_inventory)

```

![](https://img2024.cnblogs.com/blog/83005/202411/83005-20241129140502386-1675534286.gif)


# 3\. 高级用法


结合`column_config`参数，我们可以高度定制`st.data_editor`的列的显示和编辑行为。


比如：


1. **自定义列显示**：设置列的标题、帮助信息、格式等，使数据编辑器的界面更加友好和易读
2. **控制列编辑行为**：指定列的数据类型、最小值、最大值、步长等，从而控制用户对该列的编辑行为。例如，可以将一列设置为数字类型，并限制其输入范围
3. **添加特殊类型的列**：支持多种特殊类型的列，如图表列、进度条列、链接列等，这些特殊类型的列可以提供更丰富的数据展示和编辑体验


## 3\.1\. 使用示例


下面我们构建一个学生成绩管理系统，使用`st.data_editor`展示和编辑学生成绩，


并通过`st.column_config`定制列的显示和编辑行为。


下面的示例中，定义了每个列的标题，同时定义了各科目的最高分和显示的`format`。



```
import streamlit as st
import pandas as pd

# 模拟学生成绩数据
student_grades = pd.DataFrame(
    {
        "Student Name": ["Alice", "Bob", "Charlie"],
        "Math": [125, 90, 140],
        "Science": [38, 20, 34],
        "English": [88, 114, 96],
    }
)

# 定义列配置
column_config = {
    "Student Name": {"label": "学生姓名"},
    "Math": st.column_config.NumberColumn(
        label="数学成绩",
        min_value=0,
        max_value=150,
        format="%d 分",
    ),
    "Science": st.column_config.NumberColumn(
        label="科学成绩",
        min_value=0,
        max_value=40,
        format="%d 分",
    ),
    "English": st.column_config.NumberColumn(
        label="英语成绩",
        min_value=0,
        max_value=120,
        format="%d 分",
    ),
}

# 使用st.data_editor展示和编辑学生成绩，并应用列配置
st.title("学生成绩管理系统")
edited_grades = st.data_editor(
    student_grades,
    width=500,
    height=300,
    column_config=column_config,
    num_rows="dynamic",
)

# 显示编辑后的数据（用于确认更改）
st.write("编辑后的学生成绩：")
st.dataframe(edited_grades)

```

![](https://img2024.cnblogs.com/blog/83005/202411/83005-20241129140502398-673739643.gif)


下面构造一个更复杂的列的示例，其中使用了**下拉框**，**进度条**和**柱状图**作为列。


注意，**进度条**和**柱状图**的列是不能编辑的。



```
# 模拟项目数据
project_data = pd.DataFrame(
    {
        "Project Name": ["Project A", "Project B", "Project C"],
        "Status": ["In Progress", "Completed", "Pending"],
        "Progress": [60, 100, 30],  # 进度百分比
        "Sales": [
            [1000, 1500, 500],
            [200, 500, 1500],
            [1800, 500, 1000],
        ],  # 销售额
    }
)

# 定义状态选项列表
statuses = ["In Progress", "Completed", "Pending", "On Hold"]

# 定义列配置
column_config = {
    "Project Name": {"label": "项目名称"},
    "Status": st.column_config.SelectboxColumn(
        "状态",
        options=statuses,
    ),
    "Progress": st.column_config.ProgressColumn(
        "进度",
        min_value=0,
        max_value=100,
        format="%d%%",  # 显示百分比
    ),
    "Sales": st.column_config.BarChartColumn(
        "销售额变化",
        y_min=100,
        y_max=2000,
    ),
}

# 使用st.data_editor展示和编辑项目数据（不包含图表列）
st.title("项目管理系统")
edited_projects = st.data_editor(
    project_data,
    width=500,
    height=300,
    column_config=column_config,
    num_rows="dynamic",
)

```

![](https://img2024.cnblogs.com/blog/83005/202411/83005-20241129140502384-823407806.gif)


# 4\. 总结


总之，`st.data_editor`是`Streamlit`中一个功能强大的组件，它提供了类似电子表格的方式在线编辑`DataFrame`或其他类似结构的数据，直观易用。


然而，它也存在一些劣势，如**状态管理复杂性**、**性能问题**以及**定制化限制**等。


在实际应用中，需要根据具体需求权衡利弊，并结合`Streamlit`的其他组件和`Python`的强大生态来实现更复杂的数据处理和分析功能。


 本博客参考[豆荚加速器官网](https://baitenghuo.com)。转载请注明出处！
