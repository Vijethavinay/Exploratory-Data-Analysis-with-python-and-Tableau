# Covid 19 -Data-Analysis-with-python-and-Tableau

import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns 
import plotly.express as px
from datetime import datetime
from plotly.subplots import make_subplots

covid_df=pd.read_csv("F:/NCPL/Project/Python/covid_19_india.csv")
covid_df.head()
covid_df.info()
covid_df.describe()
vaccine_df=pd.read_csv("F:/NCPL/Project/Python/covid_vaccine_statewise.csv")
vaccine_df.head(10)
covid_df.drop(["Sno","Time","ConfirmedIndianNational","ConfirmedForeignNational"], inplace=True, axis=1)
covid_df['Date']=pd.to_datetime(covid_df['Date'], format= '%Y-%m-%d')
covid_df['Active_cases']=covid_df['Confirmed']-(covid_df['Cured'] + covid_df['Deaths'])
statewise=pd.pivot_table(covid_df, values=["Confirmed","Deaths","Cured"],
                         index="State/UnionTerritory",aggfunc=max)
statewise["Recovery Rate"]= statewise["Cured"]*100/statewise["Confirmed"]
statewise.style.background_gradient(cmap="cubehelix")

# Top 10 Active cases
top_10_active_cases = covid_df.groupby(by='State/UnionTerritory').max()[['Active_cases','Date']].sort_values(by=['Active_cases'], ascending=False).reset_index()
fig=plt.figure(figsize=(16,9))
plt.title("Top 10 states with most active cases in India",size=25)
ax=sns.barplot(data=top_10_active_cases.iloc[:10],y="Active_cases",x="State/UnionTerritory", linewidth =2, edgecolor='black')
plt.xlabel("States")
plt.ylabel("Total Active Cases")
plt.show()

# Top states with highest deaths

Top_10_deaths = covid_df.groupby(by='State/UnionTerritory').max()[['Deaths', 'Date']].sort_values(by=['Deaths'],ascending=False).reset_index()

fig=plt.figure(figsize=(18,5))

plt.title("Top 10 states with most Deaths",size=25)

ax=sns.barplot(data=Top_10_deaths.iloc[:12], y= "Deaths",x="State/UnionTerritory",linewidth=2, edgecolor="black")

plt.xlabel("states")
plt.ylabel("Toatal Death Cases")


# Growth Trend
fig= plt.figure(figsize = (12,6))
ax= sns.lineplot(data=covid_df[covid_df['State/UnionTerritory'].isin(['Maharashtra','Karnataka','Kerala','Tamil Nadu','Uttar Pradesh'])], x='Date',y='Active_cases',hue='State/UnionTerritory')
ax.set_title(" Top 5 Affected States in India",size =16)

vaccine_df.head()

vaccine_df.rename(columns = {'Updated On':'Vaccine_Date' }, inplace=True)
vaccine_df.head()
vaccine_df.info()
vaccine_df.isnull().sum()
vaccination=vaccine_df.drop(columns=['Sputnik V (Doses Administered)','AEFI','18-44 Years (Doses Administered)','45-60 Years (Doses Administered)','60+ Years (Doses Administered)'],axis=1)
vaccination.head()
# Male Vs Female vaccination

male=vaccination['Male(Individuals Vaccinated)'].sum()
female=vaccination['Female(Individuals Vaccinated)'].sum()
px.pie(names=["male","female"],values=[male,female],title="Male and female Vaccination")

# Remove rows where state = India
vaccine =vaccine_df[ vaccine_df.State!='India']
vaccine

vaccine.rename(columns={'Total Individuals Vaccinated': 'Total'}, inplace=True)
vaccine.head(2)

# Max Vaccinated states
max_vac=vaccine.groupby('State')['Total'].sum().to_frame("Total")
max_vac=max_vac.sort_values('Total',ascending=False)[:5]
max_vac

fig=plt.figure(figsize=(10,5))
plt.title("top 5 Vaccinated States in India", size=20)
x=sns.barplot(data=max_vac.iloc[:5],y=max_vac.Total,x=max_vac.index, linewidth=2,edgecolor='black')
plt.xlabel("States")
plt.ylabel("Vaccinations")



