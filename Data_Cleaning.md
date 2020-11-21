# 简单的数据清洗的流程
- 1、导入数据集
```
# 以csv文件为例
# 假设打开文件之后看到第一列已经有0,1,2...的索引了，因此使用index_col=0
df = pd.read_csv('文件路径', index_col=0)
```
- 2、可以大致了解一下数据的组成
    - df.shape  # 返回数据的行，列数量
    - df.info() # 对每一列进行统计，返回是否空值等
    - df.describe() # 对数值列进行统计
- 3、开始简单的数据处理
    - 1、每列最上边索引名称要规范
    ```
    df.columns  # 查看所有列名称，如果有空格等不规范数据
    col = df.columns.values
    df.columns = [x.strip() for x in col]
    ```
    - 2、查找、去除重复值
    ```
    df.duplicated()  # 按行查找重复数据，若重复标记为True
    df.duplicated().sum()  #f返回一共有多少条重复值
    
    df[df.duplicated()]  # 返回那些重复的值具体是什么
    df.drop_duplicates()  # 删除找到的重复的值
    # 删除完重复值记得检查一下每行的索引是否连续
    # 必要时使用df.index=range(df.shape[0])重置索引
    ```
    - 3、查找异常数据
    ```
    1、首先需要设置查找规则，例如
    rule = (df['价格']-df['价格'].mean())/df['价格'].std()
    df[rule.abs()>3]
    # 这里我们假定超过3倍的rule就视为异常值
    df.drop(要去掉的数据,inplace=Truw) # 删除数据,inplace是否在原数据集上修改
    ```

    - 4、查找缺失值
    ```
    df.isnull()  # 检查表中所有数据，若空则标记为True
    df.notnull()  # 与上个函数作用相反
    df.dropna()  # 删除缺失值
    df.fillna()  # 填补缺失值
  
    # 通常df.isnull().sum()用的较多
    df[df.列名.isnull()] # 找到该列是空值的所有行
    ```
    - 5、文本数据处理
        - 使用正则表达式