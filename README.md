# MIS581-Capstone-Project

This project focuses on data analysis for the organization Motion Industries.
Several Hypothesis were tested in relation to the project as displayed below;

Hypothesis 1
Ha: Ecommerce customers that go over 15 days of inactivity result in lower sales.
H0: Ecommerce customers that go over 15 days of inactivity result in zero or equal sales amounts.

Hypothesis 2
Ha: Ecommerce sales has a relationship with detailed impressions and cart additions.
H0: Ecommerce sales exhibits no relationship with detailed impressions and cart additions.

Hypothesis 3
Ha: Ecommerce sales show positive monthly trending sales.
H0: Ecommerce sales show negative monthly trending sales or no effect at all.

Several tools were used within the analysis include SAS OnDemand, Qlik, SQL and Python. 


## SAS OnDemand Analysis

###### Loading Data into SAS

	/* Generated Code (IMPORT) */
	/* Source File: MotionData.csv */
	/* Source Path: /home/u59339733/Week 6 Assignment */
	/* Code generated on: 12/1/22, 12:19 PM */
  	%web_drop_table(WORK.IMPORT);
	FILENAME REFFILE '/home/u59339733/Week 6 Assignment/MotionData.csv';
	PROC IMPORT DATAFILE=REFFILE
	DBMS=CSV
	OUT=WORK.IMPORT;
	GETNAMES=YES;
	RUN;
	PROC CONTENTS DATA=WORK.IMPORT; RUN;
	%web_open_table(WORK.IMPORT);
	/*
 	*
 	* Task code generated by SAS Studio 3.8 
 	*
 	* Generated on '12/1/22, 12:47 PM' 
 	* Generated by 'u59339733' 
 	* Generated on server 'ODAWS03-USW2.ODA.SAS.COM' 
 	* Generated on SAS platform 'Linux LIN X64 3.10.0-1062.9.1.el7.x86_64' 
	 * Generated on SAS version '9.04.01M6P11072018' 
	 * Generated on browser 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/107.0.0.0 Safari/537.36' 
 	* Generated on web client 'https://odamid-usw2.oda.sas.com/SASStudio/main?locale=en_US&zone=GMT-05%253A00&ticket=ST-128937-uJ05nNfHKWqJDqgsGFDA-cas' 
 	*
 	*/

![image](https://user-images.githubusercontent.com/119618876/205128311-f658f553-540d-49e7-8421-08533d091032.png)


###### Scatter Plot Matrix
	options validvarname=any;
	ods noproctitle;
	ods graphics / imagemap=on;
	
	/* Scatter plot matrix macro */
	%macro scatterPlotMatrix(xVars=, title=, groupVar=);
	proc sgscatter data=WORK.IMPORT1;
		matrix &xVars / %if(&groupVar ne %str()) %then
			%do;
				group=&groupVar legend=(sortorder=ascending) %end;
		;
		title &title;
	run;

![image](https://user-images.githubusercontent.com/119618876/205128351-e8a66de7-c738-4bc6-a698-ffbde3e77cca.png)


###### Correlation Analysis
	
	%mend scatterPlotMatrix;

	%scatterPlotMatrix(xVars='Cart Additions'n 'Detailed Product Views'n, 
	title="Scatter plot matrix", groupVar=);
	

	/*
 	*
 	* Task code generated by SAS Studio 3.8 
 	*
 	* Generated on '12/1/22, 12:50 PM' 
 	* Generated by 'u59339733' 
 	* Generated on server 'ODAWS03-USW2.ODA.SAS.COM' 
 	* Generated on SAS platform 'Linux LIN X64 3.10.0-1062.9.1.el7.x86_64' 
 	* Generated on SAS version '9.04.01M6P11072018' 
 	* Generated on browser 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/107.0.0.0 Safari/537.36' 
 	* Generated on web client 'https://odamid-usw2.oda.sas.com/SASStudio/main?locale=en_US&zone=GMT-05%253A00&ticket=ST-128937-uJ05nNfHKWqJDqgsGFDA-cas' 
 	*
 	*/
	ods noproctitle;
	ods graphics / imagemap=on;
	proc corr data=WORK.IMPORT1 pearson nosimple noprob plots=none;
	var 'Detailed Product Views'n;
	with 'Cart Additions'n;
	run;
	quit;

![image](https://user-images.githubusercontent.com/119618876/205128031-3f557d46-4816-49eb-af9f-81a8b8c40e46.png)

###### Linear Regression

	/*
	 *
 	* Task code generated by SAS Studio 3.8 
 	*
 	* Generated on '12/1/22, 12:53 PM' 
	* Generated by 'u59339733' 
	* Generated on server 'ODAWS03-USW2.ODA.SAS.COM' 
 	* Generated on SAS platform 'Linux LIN X64 3.10.0-1062.9.1.el7.x86_64' 
 	* Generated on SAS version '9.04.01M6P11072018' 
 	* Generated on browser 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/107.0.0.0 Safari/537.36' 
 	* Generated on web client 'https://odamid-usw2.oda.sas.com/SASStudio/main?locale=en_US&zone=GMT-05%253A00&ticket=ST-128937-uJ05nNfHKWqJDqgsGFDA-cas' 
 	*
 	*/

	ods noproctitle;
	ods graphics / imagemap=on;

	proc reg data=WORK.IMPORT1 alpha=0.05 plots(only)=(diagnostics residuals 
		fitplot observedbypredicted);
	model 'Detailed Product Views'n='Cart Additions'n /;
	run;
	quit;
	
![image](https://user-images.githubusercontent.com/119618876/205128680-d6458e60-31f1-455d-af14-f964edff195e.png)


## Python Analysis

###### Loading Librarires and Dataset
	from prophet import Prophet
	import pandas as pd
	import numpy as np
	import pandas as pd
	import statsmodels.api as sm
	import matplotlib.pyplot as plt
	from statsmodels.tsa.stattools import adfuller, kpss, acf, grangercausalitytests
	from statsmodels.graphics.tsaplots import plot_acf, plot_pacf,month_plot,quarter_plot
	from scipy import signal
	import matplotlib.pyplot as plt
	import seaborn as sns 
	%matplotlib inline 
	sns.set_style("whitegrid")
	plt.rc('xtick', labelsize=15) 
	plt.rc('ytick', labelsize=15) 
	from pathlib import Path
	my_csv = Path("C:/Users/Will/Desktop/Websales.csv")
	df = pd.read_csv(my_csv.resolve(), sep=',')

###### Data Set
	print(df)



###### Setting up X and Y axis from dataset 
x = df['Month']
y = df['Sales']

![image](https://user-images.githubusercontent.com/119618876/205128951-766630e5-faee-4895-b752-25d60a500c96.png)


###### Creating Line and Dot Plot for Trend Refrencing
	plt.xlabel('Month')
	naming the y axis
	plt.ylabel('Web Sales')  
	plotting a line plot with it's default size
	plt.plot(x, y)
	plt.show()
	plt.rcParams['figure.figsize'] = 34, 15
  
	plt.plot(x, y)
	plt.show()
  
	plt.scatter(x, y)
	plt.show()
  
  
###### Results

![image](https://user-images.githubusercontent.com/119618876/205129073-b9c9e450-5c88-44dd-916e-2fec14c5b333.png)



