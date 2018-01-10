# Kaggle common notes
### 测试数据正则性的手段
	test_normality = lambda x: stats.shprio(x.fillna(0))[1] < 0.01 #return 1 for following normal distribution
	normal = pd.DataFrame(train[quantitative])
	normal = normal.apply(test_normality)
	print(not normal.any()) #true for any that follows normal distribution
### 绘图发掘数据分布，并与经典分布拟合的方法
	import scipy.stats as st
	y = train['SalePrice']
	ptl.figure(1) ; plt.title('Johnson SU')
	sns.distplot(y , kde= False, fit=st.johnsonsu )
### 测试量化指标与定性指标的方法
	quantitative = [ f for f in train.columns if train.dtypes [f] != ‘object’ ]
	qualitative = [ f for f in train.columns if train.dtypes[f] == "object" ]
	#返回的是index数组，可以直接应用于pandas来检索
### 用于检测数据是否空缺的，并做统计的方法
	missing = train.isnull().sum() #沿着列累加，统计出每一列的空缺数据数目
	missing = missing[missing>0]
	missing.sort_values(inPlace=True)
	missing.plot.bar()
### 拟合量化数据到
