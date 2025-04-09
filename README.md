import streamlit as st
import pandas as pd
import matplotlib.pyplot as plt

# 设置页面配置
st.set_page_config(page_title="数据处理应用", layout="wide")

# 应用标题
st.title("简易数据处理应用")

# 文件上传组件
uploaded_file = st.file_uploader("上传 CSV 文件", type=["csv"])

# 如果文件上传成功，进行处理
if uploaded_file:
    # 读取 CSV 文件，并确保使用 UTF-8 编码
    df = pd.read_csv(uploaded_file, encoding='utf-8')

    # 显示原始数据预览
    st.subheader("原始数据预览")
    st.dataframe(df.head())

    # 显示数据的行列信息
    st.write(f"数据行数：{df.shape[0]} 行，列数：{df.shape[1]} 列")

    # 数值列的统计信息
    st.subheader("数值列统计信息")
    st.write(df.describe())

    # 选择要可视化的数值列
    st.subheader("选择列进行图表可视化")
    numeric_cols = df.select_dtypes(include='number').columns.tolist()
    if numeric_cols:
        selected_col = st.selectbox("选择数值列", numeric_cols)
        chart_type = st.radio("图表类型", ["柱状图", "折线图"])

        # 绘制选定列的图表
        fig, ax = plt.subplots()
        if chart_type == "柱状图":
            df[selected_col].value_counts().plot(kind="bar", ax=ax)
        else:
            df[selected_col].plot(kind="line", ax=ax)
        st.pyplot(fig)

    # 提供导出处理后 CSV 文件的选项
    st.subheader("导出处理后的 CSV")
    csv = df.to_csv(index=False).encode('utf-8')
    st.download_button("下载 CSV", csv, "processed.csv", "text/csv")

else:
    st.info("请上传一个 CSV 文件开始分析。")
