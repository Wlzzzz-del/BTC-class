# 一个交易实例
[交易实例](pic/transaction_example.png)
比特币系统中使用的脚本语言非常简单，唯一可以访问的空间只有栈，被称为基于栈的语言。

# 一笔交易的宏观信息
[交易的宏观信息](pic/transaction_struct.png)

# 交易的输入
[交易的输入](pic/transaction_input.png)

# 交易的输出
[交易的输出](pic/transaction_output.png)

# 输入和输出脚本
[交易的输入和输出脚本](pic/input_output_script.png)
在早期，将两个脚本拼接在一起.后来改为先执行input，再执行output。
如果能无误执行，最终栈顶结果为true，则验证通过交易合法。为false则交易非法。